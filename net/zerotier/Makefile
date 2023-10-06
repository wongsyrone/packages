#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=zerotier
PKG_VERSION:=1.12.2
PKG_RELEASE:=9ae8b0b3

#PKG_SOURCE_PROTO:=git
# dev branch is the latest
#PKG_SOURCE_URL:=https://github.com/zerotier/ZeroTierOne.git
#PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE_VERSION:=9ae8b0b3b60b27cf06d7e74629c17e4a0f248364
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION)-$(PKG_SOURCE_VERSION).tar.gz

PKG_SOURCE_URL:=https://codeload.github.com/zerotier/ZeroTierOne/tar.gz/$(PKG_SOURCE_VERSION)?
PKG_HASH:=e202c552788216dac850190ac704001d57e75971b8fb639c14f1022c368fefdf
PKG_BUILD_DIR:=$(BUILD_DIR)/ZeroTierOne-$(PKG_SOURCE_VERSION)

PKG_MAINTAINER:=Moritz Warning <moritzwarning@web.de>
PKG_LICENSE:=BSL 1.1
PKG_LICENSE_FILES:=LICENSE.txt

PKG_ASLR_PIE:=0
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=gc-sections

include $(INCLUDE_DIR)/package.mk

define Package/zerotier
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+libpthread +libstdcpp +kmod-tun +ip +libminiupnpc +libnatpmp +libatomic
  TITLE:=Create flat virtual Ethernet networks of almost unlimited size
  URL:=https://www.zerotier.com
  SUBMENU:=VPN
endef

define Package/zerotier/description
	ZeroTier creates a global provider-independent virtual private cloud network.
endef

define Package/zerotier/config
	source "$(SOURCE)/Config.in"
endef

ifeq ($(CONFIG_ZEROTIER_ENABLE_DEBUG),y)
MAKE_FLAGS += ZT_DEBUG=1
endif

MAKE_FLAGS += \
	ZT_EMBEDDED=1 \
	ZT_SSO_SUPPORTED=0 \
	DEFS="" \
	OSTYPE="Linux" \

define Build/Compile
	$(call Build/Compile/Default,one)
ifeq ($(CONFIG_ZEROTIER_ENABLE_SELFTEST),y)
	$(call Build/Compile/Default,selftest)
endif
endef

# all warnings and don't treat all of them as error, I don't want to fix them all
TARGET_CFLAGS += -Wall

# Make binary smaller
TARGET_CFLAGS += -Wl,-z,noexecstack
TARGET_LDFLAGS += -Wl,--as-needed -Wl,-z,noexecstack

define Package/zerotier/conffiles
/etc/config/zerotier
/etc/zerotier
endef

define Package/zerotier/install
	$(INSTALL_DIR) $(1)/usr/bin $(1)/etc/zerotier
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/zerotier-one $(1)/usr/bin/
	$(LN) zerotier-one $(1)/usr/bin/zerotier-cli
	$(LN) zerotier-one $(1)/usr/bin/zerotier-idtool

ifeq ($(CONFIG_ZEROTIER_ENABLE_SELFTEST),y)
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/zerotier-selftest $(1)/usr/bin/
endif
	#moved to luci-app-zerotier

	#$(CP) ./files/* $(1)/
endef

$(eval $(call BuildPackage,zerotier))
