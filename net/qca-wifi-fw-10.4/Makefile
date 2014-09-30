#
# Copyright (c) 2014 Qualcomm Atheros, Inc..
#
# All Rights Reserved.
# Qualcomm Atheros Confidential and Proprietary.
#

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG:=qca-wifi-fw
PKG_NAME:=$(PKG)-10.4
PKG_VERSION:=CNSS.BL.1.0-00271-S-1
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG)$(BUILD_VARIANT)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=http://tritium.qca.qualcomm.com/software/NBU/CNSS.BL.1.0/$(PKG_VERSION)/src
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG)-$(PKG_VERSION)
include $(INCLUDE_DIR)/package-version-override.mk
include $(INCLUDE_DIR)/package.mk

define WifiFirmware
  define Package/$(PKG_NAME)$(1)
    SECTION:=QCA
    CATEGORY:=QCA Proprietary software
    URL:=http://www.qca.qualcomm.com
    MAINTAINER:=Qualcomm Atheros
    TITLE:=QCA Wifi Firmware$(1)
    VARIANT:=$(1)
  endef

  define Package/$(PKG_NAME)$(1)/description
    This package contains the qca-wifi firmware($(1)) used in 10.x drivers
  endef

  define Package/$(PKG_NAME)$(1)/config
  $(call Package/$(PKG_NAME)/override_source_path,$(PKG_NAME)$(1))
  endef

  define Package/$(PKG_NAME)$(1)/install
	$(INSTALL_DIR) $$(1)/lib/firmware
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/*.bin $$(1)/lib/firmware
  endef

  $$(eval $$(call BuildPackage,$(PKG_NAME)$(1)))
endef

Build/Compile=true

$(eval $(call WifiFirmware,-emu))
$(eval $(call WifiFirmware,-asic))
