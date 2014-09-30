#
# Copyright (c) 2014 Qualcomm Atheros, Inc.
#
# All Rights Reserved.
# Qualcomm Atheros Confidential and Proprietary.
#

include $(TOPDIR)/rules.mk

PKG:=qca-acfg
PKG_NAME:=$(PKG)-10.4

PKG_VERSION:=10.4.268
PKG_RELEASE:=1

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG)-$(PKG_VERSION)
PKG_SOURCE:=$(PKG)-$(PKG_VERSION).tar.bz2

include $(INCLUDE_DIR)/package-version-override.mk
include $(INCLUDE_DIR)/package.mk

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG)-$(PKG_VERSION)
PKG_BRANCH:=$(basename $(PKG_VERSION))
PKG_SOURCE_URL:=http://tritium.qca.qualcomm.com/software/$(PKG_BRANCH)/AP$(PKG_VERSION:999.999.0%=QCAMain%)/src/components
PKG_BUILD_DEPENDS:=qca-wifi-10.4

define Package/$(PKG_NAME)
  SECTION:=QCA
  CATEGORY:=QCA Proprietary software
  URL:=http://www.qca.qualcomm.com
  MAINTAINER:=Qualcomm Atheros
  TITLE:=QCA Configuration Utility
  DEPENDS:=kmod-qca-wifi-10.4 @(!(PACKAGE_hostapd))
endef

define Package/$(PKG_NAME)/config
$(call Package/$(PKG_NAME)/override_source_path,$(PKG))
$(call Package/$(PKG_NAME)/override_version,$(PKG),$(PKG_SUPPORTED_VERSION))
endef

define Package/$(PKG_NAME)/description
 QCA configuration utility provides a set of library
 API's through which user applications can configure
 and register events for wifi driver and hostap
endef

MAKEOPTS+= \
	$(TARGET_CONFIGURE_OPTS) \
	WLAN_TOP=$(STAGING_DIR)/usr \
	COPTS="$(TARGET_CFLAGS) -I. -I$(PKG_BUILD_DIR)/acfg/include" \
	QSDK_ACFG=1

define Build/Compile
	$(MAKE) -C $(PKG_BUILD_DIR)/acfg/src $(MAKEOPTS)
	$(MAKE) -C $(PKG_BUILD_DIR)/acfg_test_app $(MAKEOPTS) LIB_OUT=acfg
endef

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/acfg_test_app/acfg_tool $(1)/usr/sbin
endef

$(eval $(call BuildPackage,$(PKG_NAME)))
