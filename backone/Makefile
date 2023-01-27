#
# Copyright (C) 2016 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v3.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=backone
PKG_VERSION:=1.1.14
PKG_REV:=8c73dbdecc1662fed615225940d4834c9654b286
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=https:///github.com/proitlab/BackOne.git
PKG_SOURCE_PROTO:=git
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE_VERSION:=$(PKG_REV)

PKG_LICENSE:=GPL-3.0

PKG_BUILD_DEPENDS:=uclibcxx
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define Package/backone
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+libpthread +kmod-tun +ip
  TITLE:=Create flat virtual Ethernet networks of almost unlimited size
  URL:=https://backone.cloud
  SUBMENU:=VPN
endef

define Package/backone/description
	BackOne creates a global provider-independent virtual private cloud network.
endef

define Package/backone/config
	source "$(SOURCE)/Config.in"
endef

ifeq ($(CONFIG_BACKONE_ENABLE_NETWORK_CONTROLLER),y)
MAKE_FLAGS += ZT_ENABLE_NETWORK_CONTROLLER=1
DEPENDS+=+sqlite
endif

ifeq ($(CONFIG_BACKONE_USE_MINIUPNPC),y)
MAKE_FLAGS += ZT_USE_MINIUPNPC=1
DEPENDS+=+libminiupnpc
endif

ifeq ($(CONFIG_BACKONE_ENABLE_DEBUG),y)
TARGET_CXXFLAGS += -DZT_TRACE -ggdb3
endif

MAKE_FLAGS += \
	LDFLAGS+=" -L$(STAGING_DIR)/usr/lib/uClibc++ -pthread " \
	LDLIBS+=" -fno-builtin -nodefaultlibs -Wl,-Bstatic -luClibc++ -Wl,-Bdynamic  -lpthread -lm -lc -lsupc++ -lc -lgcc -lgcc_eh -lgcc_s  -lpthread -lm " \
	CXXFLAGS+=" -fno-builtin -nostdinc++ -I$(STAGING_DIR)/usr/include/uClibc++ -DGCC_HASCLASSVISIBILITY -Wall -fPIE -fvisibility=hidden "

define Build/Compile
	$(call Build/Compile/Default,one)
ifeq ($(CONFIG_BACKONE_ENABLE_SELFTEST),y)
	$(call Build/Compile/Default,selftest)
endif
endef

define Package/backone/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/backone $(1)/usr/bin/
	$(LN) backone $(1)/usr/bin/backone-cli
	$(LN) backone $(1)/usr/bin/backone-idtool

ifeq ($(CONFIG_BACKONE_ENABLE_SELFTEST),y)
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/backone-selftest $(1)/usr/bin/
endif

	$(INSTALL_DIR) $(1)/etc/init.d/
	$(INSTALL_BIN) files/backone.init $(1)/etc/init.d/backone
	$(INSTALL_DIR) $(1)/etc/config
	$(INSTALL_CONF) files/backone.config $(1)/etc/config/backone
endef

$(eval $(call BuildPackage,backone))

