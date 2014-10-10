#
# Copyright (c) 2013 Qualcomm Atheros, Inc..
#
# All Rights Reserved.
# Qualcomm Atheros Confidential and Proprietary.
#

include $(TOPDIR)/rules.mk

PKG:=qca-hostap
PKG_NAME:=$(PKG)-10.4

# This value is custom to this package
# The first version will be used as a default and the other selectable
# in the menuconfig
PKG_VERSION:=10.4.268
PKG_RELEASE:=1

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG)-$(PKG_VERSION)
PKG_SOURCE:=$(PKG)-$(PKG_VERSION).tar.bz2

include $(INCLUDE_DIR)/package-version-override.mk
include $(INCLUDE_DIR)/package.mk

PKG_BRANCH:=$(basename $(PKG_VERSION))
PKG_SOURCE_URL:=http://tritium.qca.qualcomm.com/software/$(PKG_BRANCH)/AP$(PKG_VERSION:999.999.0%=QCAMain%)/src/components
PKG_BUILD_DEPENDS:=qca-wifi-10.4

LOCAL_TYPE=$(strip \
		$(if $(findstring qca-wpa-supplicant,$(BUILD_VARIANT)),supplicant, \
		hostapd \
	)))

define Package/$(PKG_NAME)/Default
  SECTION:=QCA
  CATEGORY:=QCA Proprietary software
  URL:=http://www.qca.qualcomm.com
  MAINTAINER:=Qualcomm Atheros
endef

define Package/$(PKG_NAME)
  $(call Package/$(PKG_NAME)/Default)
  TITLE:=Hostap authenticator
  DEPENDS:=kmod-qca-wifi-10.4 @(!(PACKAGE_hostapd))
endef

define Package/$(PKG_NAME)/description
 This package contains the IEEE 802.1x/WPA/EAP/RADIUS Authenticator.
endef

define Package/qca-hostapd-cli-10.4
  $(call Package/$(PKG_NAME)/Default)
  DEPENDS:=$(PKG_NAME)
  TITLE:=Hostap authenticator command line interface
endef

define Package/qca-hostapd-cli-10.4/description
 This package contains a command line utility to control the
 IEEE 802.1x/WPA/EAP/RADIUS Authenticator.
endef

define Package/qca-wpa-supplicant-10.4
  $(call Package/$(PKG_NAME)/Default)
  TITLE:=WPA Supplicant
  DEPENDS:=kmod-qca-wifi-10.4 @(!(PACKAGE_wpa-supplicant))
endef

define Package/qca-wpa-supplicant/Description
  WPA Supplicant
endef

define Package/qca-wpa-cli-10.4
  $(call Package/$(PKG_NAME)/Default)
  DEPENDS:=qca-wpa-supplicant-10.4
  TITLE:=WPA Supplicant command line interface
endef

define Package/qca-wpa-cli-10.4/Description
  WPA Supplicant control utility
endef


ifneq ($(wildcard $(PKG_BUILD_DIR)/.config_*),$(subst .configured_,.config_,$(STAMP_CONFIGURED)))
  define Build/Configure/rebuild
	$(FIND) $(PKG_BUILD_DIR) -name \*.o -or -name \*.a | $(XARGS) rm -f
	rm -f $(PKG_BUILD_DIR)/hostapd/hostapd
	rm -f $(PKG_BUILD_DIR)/wpa_supplicant/wpa_supplicant
	rm -f $(PKG_BUILD_DIR)/.config_*
	touch $(subst .configured_,.config_,$(STAMP_CONFIGURED))
  endef
endif

define ConfigOptAdd
	config QCA_$(3)_$(1)
			bool "$(patsubst $(2)-%,%,$(1))"
endef

define ConfigAthrHostap
	choice
		prompt "$(1) configuration"
		depends on PACKAGE_qca-$(1)-10.4
	$(foreach config,$(notdir $(basename $(wildcard files/$(1)-*.config))),
		$(call ConfigOptAdd,$(config),$(1),$(2)))
	endchoice

	config QCA_$(2)_CONFIG
		string
	$(foreach config,$(notdir $(basename $(wildcard files/$(1)-*.config))),
		default "$(config)" if QCA_$(2)_$(config))

endef

define Package/$(PKG_NAME)/config
$(call Package/$(PKG_NAME)/override_source_path,$(PKG))
$(call Package/$(PKG_NAME)/override_version,$(PKG),$(PKG_SUPPORTED_VERSION))
$(call ConfigAthrHostap,hostap,ATHR_HOSTAP_HOSTAPD)
endef

define Package/qca-wpa-supplicant-10.4/config
$(call ConfigAthrHostap,wpa-supplicant,ATHR_HOSTAP_SUPPLICANT)
endef

HOSTAPD_CONFIG_FILE:= \
	./files/$(strip $(CONFIG_QCA_ATHR_HOSTAP_HOSTAPD_CONFIG)).config
WPA_CONFIG_FILE= \
	./files/$(strip $(CONFIG_QCA_ATHR_HOSTAP_SUPPLICANT_CONFIG)).config

define Build/Configure
	$(Build/Configure/rebuild)
	$(CP) $(HOSTAPD_CONFIG_FILE) $(PKG_BUILD_DIR)/hostapd/.config
	$(CP) $(WPA_CONFIG_FILE) $(PKG_BUILD_DIR)/wpa_supplicant/.config
endef

TARGET_CPPFLAGS += \
	-I$(STAGING_DIR)/usr/include

define Build/RunClean
	$(MAKE) -C $(PKG_BUILD_DIR)/$(1) clean
endef

define Build/RunMake
	CFLAGS="$(TARGET_CPPFLAGS) $(TARGET_CFLAGS)" \
	$(MAKE) -C $(PKG_BUILD_DIR)/$(1) \
	$(TARGET_CONFIGURE_OPTS) \
	LIBS="$(TARGET_LDFLAGS)" \
	$(2)
endef

define Build/Compile/$(PKG_NAME)
	$(FIND) $(PKG_BUILD_DIR) -name \*.o -or -name \*.a | $(XARGS) rm -f
	$(call Build/RunClean,hostapd)
	$(call Build/RunMake,hostapd, \
		hostapd hostapd_cli \
	)
endef

define Build/Compile/qca-wpa-supplicant-10.4
	$(FIND) $(PKG_BUILD_DIR) -name \*.o -or -name \*.a | $(XARGS) rm -f
	$(call Build/RunClean,wpa_supplicant)
	$(call Build/RunMake,wpa_supplicant, \
		wpa_cli wpa_supplicant \
	)
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_BUILD_DIR)/src/common/wpa_ctrl.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/common/ieee802_11_defs.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/utils/includes.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/utils/build_config.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/utils/eloop.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/utils/os.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/utils/common.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/utils/wpa_debug.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/utils/wpabuf.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/drivers/linux_ioctl.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/drivers/linux_wext.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/drivers/netlink.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/drivers/priv_netlink.h $(1)/usr/include/
	$(CP) $(PKG_BUILD_DIR)/src/common/wpa_ctrl.o $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/src/drivers/netlink.o $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/src/utils/eloop.o $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/src/utils/wpa_debug.o $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/src/utils/common.o $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/src/utils/os_unix.o $(1)/usr/lib/
endef


define Build/Compile
	$(Build/Compile/qca-wpa-supplicant-10.4)
	$(Build/Compile/$(PKG_NAME))
endef

define Install/$(PKG_NAME)
	$(INSTALL_DIR) $(1)/lib/wifi
	$(INSTALL_DATA) ./files/hostapd.sh $(1)/lib/wifi/hostapd.sh
	$(INSTALL_DIR) $(1)/usr/sbin
endef

define Install/qca-wpa-supplicant-10.4
	$(INSTALL_DIR) $(1)/lib/wifi
	$(INSTALL_DATA) ./files/wpa_supplicant.sh $(1)/lib/wifi/wpa_supplicant.sh
	$(INSTALL_DIR) $(1)/usr/sbin
endef

define Package/$(PKG_NAME)/install
	$(call Install/$(PKG_NAME),$(1))
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/hostapd/hostapd $(1)/usr/sbin/
endef

ifneq ($(LOCAL_TYPE),supplicant)
  define Package/qca-hostapd-cli-10.4/install
	$(INSTALL_DIR) $(1)/usr/sbin $(1)/etc/hotplug.d/button
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/hostapd/hostapd_cli $(1)/usr/sbin/
	$(INSTALL_DATA) ./files/wps-hotplug.sh $(1)/etc/hotplug.d/button/50-wps
	$(INSTALL_DATA) ./files/wps-reset-hotplug.sh $(1)/etc/hotplug.d/button/51-wps-reset

	$(INSTALL_DIR) $(1)/lib/wifi
	$(INSTALL_BIN) ./files/wps-hostapd-update-uci.sh \
			$(1)/lib/wifi/wps-hostapd-update-uci
  endef
endif

define Package/qca-wpa-supplicant-10.4/install
	$(call Install/qca-wpa-supplicant-10.4,$(1))
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/wpa_supplicant/wpa_supplicant $(1)/usr/sbin/
endef

ifneq ($(LOCAL_TYPE),hostapd)
  define Package/qca-wpa-cli-10.4/install
	$(INSTALL_DIR) $(1)/usr/sbin
	$(CP) $(PKG_BUILD_DIR)/wpa_supplicant/wpa_cli $(1)/usr/sbin/

	$(INSTALL_DIR) $(1)/etc/hotplug.d/button
	$(INSTALL_DATA) ./files/wps-supplicant-hotplug.sh \
			$(1)/etc/hotplug.d/button/52-wps-supplicant

	$(INSTALL_DIR) $(1)/lib/wifi
	$(INSTALL_BIN) ./files/wps-supplicant-update-uci.sh \
			$(1)/lib/wifi/wps-supplicant-update-uci
  endef
endif

$(eval $(call BuildPackage,$(PKG_NAME)))
$(eval $(call BuildPackage,qca-wpa-supplicant-10.4))
$(eval $(call BuildPackage,qca-hostapd-cli-10.4))
$(eval $(call BuildPackage,qca-wpa-cli-10.4))
