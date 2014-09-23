#
# Copyright (c) 2014 Qualcomm Atheros, Inc.
#
# All Rights Reserved.
# Qualcomm Atheros Confidential and Proprietary.
#

include $(TOPDIR)/rules.mk

PKG:=qca-wrapd
PKG_NAME:=$(PKG)-10.4

PKG_VERSION:=10.4.261
PKG_RELEASE:=1

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG)-$(PKG_VERSION)
PKG_SOURCE:=$(PKG)-$(PKG_VERSION).tar.bz2

include $(INCLUDE_DIR)/package-version-override.mk
include $(INCLUDE_DIR)/package.mk

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG)-$(PKG_VERSION)
PKG_BRANCH:=$(basename $(PKG_VERSION))
PKG_SOURCE_URL:=http://tritium.qca.qualcomm.com/software/$(PKG_BRANCH)/AP$(PKG_VERSION:999.999.0%=QCAMain%)/src/components
PKG_BUILD_DEPENDS:=qca-hostap-10.4

define Package/$(PKG_NAME)
  SECTION:=QCA
  CATEGORY:=QCA Proprietary software
  URL:=http://www.qca.qualcomm.com
  MAINTAINER:=Qualcomm Atheros
  TITLE:=QCA wrapd daemon
  DEPENDS:=qca-hostap-10.4 @(!(PACKAGE_hostapd))
endef

define Package/$(PKG_NAME)/config
$(call Package/$(PKG_NAME)/override_source_path,$(PKG))
$(call Package/$(PKG_NAME)/override_version,$(PKG),$(PKG_SUPPORTED_VERSION))
endef

define Package/$(PKG_NAME)/description
  User Space application to support QWRAP feature
endef

MAKEOPTS+= \
	$(TARGET_CONFIGURE_OPTS) \
	COPTS="$(TARGET_CFLAGS)" \
	WPA_PATH=$(STAGING_DIR)/usr/lib \
	QSDK_WRAPD=$(STAGING_DIR)/usr/include

define Build/Compile
	$(MAKE) -C $(PKG_BUILD_DIR) $(MAKEOPTS)
endef

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/wrapd $(1)/usr/sbin

	$(INSTALL_DIR) $(1)/etc/ath
	$(INSTALL_DATA) ./files/wrap-vma.conf $(1)/etc/ath
endef

$(eval $(call BuildPackage,$(PKG_NAME)))
