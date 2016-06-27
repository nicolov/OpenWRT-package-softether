# 
# Copyright (C) 2006 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=softethervpn
PKG_VERSION:=4.19
PKG_RELEASE:=9605
#PKG_VERSION2:=rtm
PKG_VERSION2:=beta
PKG_DATE:=2016.03.06

PKG_BUILD_DIR:=$(BUILD_DIR)/v$(PKG_VERSION)-$(PKG_RELEASE)
PKG_SOURCE:=softether-src-v$(PKG_VERSION)-$(PKG_RELEASE)-$(PKG_VERSION2).tar.gz
PKG_SOURCE_URL:=http://jp.softether-download.com/files/softether/v$(PKG_VERSION)-$(PKG_RELEASE)-$(PKG_VERSION2)-$(PKG_DATE)-tree/Source_Code/
PKG_MD5SUM:=59ae7ecfce22936b51621b5d860152c1

PKG_BUILD_DEPENDS += softethervpn/host
HOST_BUILD_DIR:=$(BUILD_DIR_HOST)/v$(PKG_VERSION)-$(PKG_RELEASE)
HOST_BUILD_DEPENDS:=libiconv/host

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

define Package/softethervpn
	SECTION:=net
	CATEGORY:=Network
	SUBMENU:=VPN
	TITLE:=Open-Source Free Cross-platform Multi-protocol VPN
	URL:=http://www.softether.org/
	DEPENDS:=$(ICONV_DEPENDS) +libpthread +librt +libreadline +libopenssl +libncurses +kmod-tun
endef

define Package/softethervpn/conffiles
	/usr/bin/vpn_bridge.config
	/usr/bin/vpn_client.config
	/usr/bin/vpn_server.config
endef

#define Package/softethervpnclient
#	$(call Package/softethervpnserver)
#endef
#
#define Package/softethervpnbridge
#	$(call Package/softethervpnserver)
#endef
#
#define Package/softethervpncmd
#	$(call Package/softethervpnserver)
#endef

ifeq ($(ARCH),mips)
	SE4WRT_OPTIONS := -minterlink-mips16
endif
ifeq ($(ARCH),mipsel)
	SE4WRT_OPTIONS := -minterlink-mips16
endif

HAMCORE_REL_PATH:=src/bin/BuiltHamcoreFiles/unix/
HAMCORE_REL_FILE:=$(HAMCORE_REL_PATH)hamcore.se2

define Build/Prepare
	$(call Build/Prepare/Default)

	# Uglily move the built hamcore.se2 into the cross compilation folder
	mkdir -p $(PKG_BUILD_DIR)/$(HAMCORE_REL_PATH)
	$(CP) $(HOST_BUILD_DIR)/$(HAMCORE_REL_FILE) $(PKG_BUILD_DIR)/$(HAMCORE_REL_PATH)
	touch -d "`date -d 1day`" $(PKG_BUILD_DIR)/$(HAMCORE_REL_FILE)
endef

define Build/Configure
	$(CP) $(PKG_BUILD_DIR)/src/makefiles/linux_32bit.mak $(PKG_BUILD_DIR)/Makefile
endef

define Build/Compile
	# Iconv stuuf
	$(warning $(ICONV_PREFIX))
	$(warning $(ICONV_LDFLAGS))
	$(call Build/Compile/Default,LDFLAGS="$(ICONV_LDFLAGS) -liconv")
endef

#define Package/softethervpnserver/install
define Package/softethervpn/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_DIR) $(1)/etc/init.d
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/bin/vpnserver/* $(1)/usr/bin
	ln -s vpnserver $(1)/usr/bin/vpnclient
	ln -s vpnserver $(1)/usr/bin/vpnbridge
	ln -s vpnserver $(1)/usr/bin/vpncmd
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/rc/vpnserver $(1)/etc/init.d/softethervpnserver
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/rc/vpnclient $(1)/etc/init.d/softethervpnclient
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/rc/vpnbridge $(1)/etc/init.d/softethervpnbridge
endef

#define Package/softethervpnclient/install
#	$(INSTALL_DIR) $(1)/usr/bin
#	$(INSTALL_BIN) $(PKG_BUILD_DIR)/bin/vpnclient/* $(1)/usr/bin
#endef
#
#define Package/softethervpnbridge/install
#	$(INSTALL_DIR) $(1)/usr/bin
#	$(INSTALL_DIR) $(1)/etc/init.d
#	$(INSTALL_BIN) $(PKG_BUILD_DIR)/bin/vpnbridge/* $(1)/usr/bin
#	$(INSTALL_BIN) $(PKG_BUILD_DIR)/rc/vpnbridge $(1)/etc/init.d/softethervpnbridge
#endef
#
#define Package/softethervpncmd/install
#	$(INSTALL_DIR) $(1)/usr/bin
#	$(INSTALL_BIN) $(PKG_BUILD_DIR)/bin/vpncmd/* $(1)/usr/bin
#endef

define Host/Configure
	$(CP) $(HOST_BUILD_DIR)/src/makefiles/linux_32bit.mak $(HOST_BUILD_DIR)/Makefile
endef

define Host/Prepare
	$(call Host/Prepare/Default)

	# Delete some files to save space
	cd $(HOST_BUILD_DIR)/src/bin/hamcore/
	mkdir -p $(HOST_BUILD_DIR)/src/bin/hamcore_minimal
	mv $(HOST_BUILD_DIR)/src/bin/hamcore/{legal.txt,openvpn_sample.ovpn,root_certs.dat,strtable_en.stb} $(HOST_BUILD_DIR)/src/bin/hamcore_minimal
	rm -rf $(HOST_BUILD_DIR)/src/bin/hamcore
	mv $(HOST_BUILD_DIR)/src/bin/hamcore_minimal $(HOST_BUILD_DIR)/src/bin/hamcore
endef

define Host/Compile
	# Build hamcore.se2 on the host, using the host hamcorebuilder
	$(call Host/Compile/Default,$(HAMCORE_REL_FILE))
endef

define Host/Install
endef


$(eval $(call HostBuild))
$(eval $(call BuildPackage,softethervpn))
#$(eval $(call BuildPackage,softethervpnserver))
#$(eval $(call BuildPackage,softethervpnclient))
#$(eval $(call BuildPackage,softethervpnbridge))
#$(eval $(call BuildPackage,softethervpncmd))
