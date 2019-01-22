#
# Author: Xiangfu Liu <xiangfu@openmobilefree.net>
# Address: 12h6gdGnThW385JaX1LRMA8cXKmbYRTP8Q
#
# This is free and unencumbered software released into the public domain.
# For details see the UNLICENSE file at the root of the source tree.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=cgminer

ifeq ($(CONFIG_CGMINER_GEKKO),y)
       #PKG_VERSION:=git-$(shell git ls-remote https://github.com/csa402/cgminer-gekko.git master | cut -f1 | cut -c1-7)
       PKG_VERSION:=git-$(shell git ls-remote https://github.com/wareck/cgminer-gekko.git master | cut -f1 | cut -c1-7)
       PKG_REV:=master
endif

PKG_RELEASE:=1
PKG_INSTALL:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION)-$(PKG_REV).tar.bz2
ifeq ($(CONFIG_CGMINER_GEKKO),y)
	#PKG_SOURCE_URL:=git://github.com/csa402/cgminer-gekko.git
	PKG_SOURCE_URL:=git://github.com/wareck/cgminer-gekko.git
endif

PKG_SOURCE_PROTO:=git
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_SOURCE_VERSION:=$(PKG_REV)

PKG_FIXUP:=autoreconf

include $(INCLUDE_DIR)/package.mk

define Package/cgminer
	SECTION:=utils
	CATEGORY:=Utilities
	TITLE:=cgminer
	URL:=https://github.com/ckolivas/cgminer
ifeq ($(CONFIG_TARGET_brcm2708_RaspberryPi),)
	DEPENDS:=+libcurl +libpthread +jansson +udev
else
	DEPENDS:=+libcurl +libpthread +jansson +udev +libncurses
endif
endef

define Package/cgminer/description
Cgminer is a multi-threaded multi-pool GPU, FPGA and CPU miner with ATI GPU
monitoring, (over)clocking and fanspeed support for bitcoin and derivative
coins. Do not use on multiple block chains at the same time!
endef

define Package/cgminer/config
	menu "Configuration"
	depends on PACKAGE_cgminer
	source "$(SOURCE)/Config.in"
	endmenu
endef

TARGET_LDFLAGS += -Wl,-rpath-link=$(STAGING_DIR)/usr/lib

ifeq ($(CONFIG_CGMINER_GEKKO),y)
	CONFIGURE_ARGS += --enable-gekko --enable-bflsc
endif
ifeq ($(CONFIG_TARGET_brcm2708_RaspberryPi),)
	CONFIGURE_ARGS += --without-curses
endif

define Build/Compile
	$(call Build/Compile/Default)
	(cd $(PKG_BUILD_DIR) && \
	 $(TARGET_CC) -Icompat/jansson -Icompat/libusb-1.0/libusb \
	api-example.c -o cgminer-api;)

endef

define Package/cgminer/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_DIR) $(1)/etc/config

	$(INSTALL_BIN) $(PKG_BUILD_DIR)/cgminer-api	   $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/cgminer  $(1)/usr/bin

ifeq ($(CONFIG_CGMINER_GEKKO),y)
	$(INSTALL_BIN) $(FILES_DIR)/cgminer-gekko-monitor        $(1)/usr/bin/cgminer-monitor
	$(INSTALL_BIN) $(FILES_DIR)/cgminer.gekko.init           $(1)/etc/init.d/cgminer
	$(CP)          $(FILES_DIR)/cgminer.gekko.config         $(1)/etc/config/cgminer
endif
endef

$(eval $(call BuildPackage,cgminer))
