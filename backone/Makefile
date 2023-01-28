#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=backone
PKG_VERSION:=1.6.6
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/zerotier/ZeroTierOne/tar.gz/$(PKG_VERSION)?
PKG_HASH:=a437ec9e8a4987ed48c0e5af3895a057dcc0307ab38af90dd7729a131097f222
PKG_BUILD_DIR:=$(BUILD_DIR)/BackOne-$(PKG_VERSION)

PKG_MAINTAINER:=Dedy Sutanto <dedy.sutanto@proit.co.id>
PKG_LICENSE:=BSL 1.1
PKG_LICENSE_FILES:=LICENSE.txt

PKG_ASLR_PIE:=0
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define Package/backone
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+libpthread +libstdcpp +kmod-tun +ip +libminiupnpc +libnatpmp
  TITLE:=Create flat virtual Ethernet networks of almost unlimited size
  URL:=https://backone.cloud
  SUBMENU:=VPN
endef

define Package/backone/description
	BackOne global ethernet switch.
endef

define Package/backone/config
	source "$(SOURCE)/Config.in"
endef

ifeq ($(CONFIG_BACKONE_ENABLE_DEBUG),y)
MAKE_FLAGS += ZT_DEBUG=1
endif

MAKE_FLAGS += \
	DEFS="" \

define Build/Compile
	$(call Build/Compile/Default,one)
ifeq ($(CONFIG_BACKONE_ENABLE_SELFTEST),y)
	$(call Build/Compile/Default,selftest)
endif
endef

# Make binary smaller
TARGET_CFLAGS += -ffunction-sections -fdata-sections
TARGET_LDFLAGS += -Wl,--gc-sections,--as-needed

define Package/backone/conffiles
/etc/config/backone
endef

define Package/backone/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/backone $(1)/usr/bin/
	$(LN) backone $(1)/usr/bin/backone-cli
	$(LN) backone $(1)/usr/bin/backone-idtool

ifeq ($(CONFIG_BACKONE_ENABLE_SELFTEST),y)
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/backone-selftest $(1)/usr/bin/
endif

	$(CP) ./files/* $(1)/
endef

$(eval $(call BuildPackage,backone))

