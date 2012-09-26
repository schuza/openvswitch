# 
# Copyright (C) 2009 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# $Id: Makefile $

include $(TOPDIR)/rules.mk

PKG_NAME_SHORT:=openvswitch
PKG_NAME:=$(PKG_NAME_SHORT)

#PKG_VERSION:=1.4.2
#PKG_REV:=b9a30fe2ebb1d300178d6ba7628b3dc40ff1512b
PKG_RELEASE:=1
PKG_VERSION:=1.4.3

#PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
#PKG_SOURCE_URL:=git://openvswitch.org/openvswitch
PKG_SOURCE_URL:=http://openvswitch.org/releases/
#PKG_SOURCE_PROTO:=git
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
#PKG_SOURCE_VERSION:=$(PKG_REV)

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_FIXUP=libtool

define Package/openvswitch/Default
  SECTION:=net
  CATEGORY:=Network
  URL:=http://www.openvswitchswitch.org/
  DEPENDS:=+libopenssl +librt
  MAINTAINER:=Julius Schulz-Zander <julius@net.t-labs.tu-berlin.de>
endef

define Package/openvswitch/Default/description
  Open vSwitch is a production quality, multilayer, software-based, Ethernet
  virtual switch. It is designed to enable massive network automation through
  programmatic extension, while still supporting standard management interfaces
  and protocols (e.g. NetFlow, sFlow, SPAN, RSPAN, CLI, LACP, 802.1ag). In
  addition, it is designed to support distribution across multiple physical
  servers similar to VMware's vNetwork distributed vswitch or Cisco's Nexus
  1000V.
endef

define Package/openvswitch-common
  $(call Package/openvswitch/Default)
  TITLE:=Open Flow Switch Userspace Package
  DEPENDS:=+libopenssl +librt +kmod-openvswitch
endef

define Package/openvswitch-common/description
  openvswitch-common provides components required by both openvswitch-switch and openvswitch-controller.
endef

define Package/openvswitch-ipsec
  $(call Package/openvswitch/Default)
  TITLE:=Open Flow Switch Userspace Package
  DEPENDS:=+openvswitch-common
endef

define Package/openvswitch-ipsec/description
  The ovs-monitor-ipsec script provides support for encrypting GRE tunnels with IPsec.
endef

define Package/openvswitch-controller
  $(call Package/openvswitch/Default)
  TITLE:=Open Flow Switch Userspace Package
  DEPENDS:=+openvswitch-common
endef

define Package/openvswitch-controller/description
  The Open vSwitch controller enables OpenFlow switches that connect to it to
  act as MAC-learning Ethernet switches.
endef

define Package/openvswitch-switch
  $(call Package/openvswitch/Default)
  TITLE:=Open Flow Switch Userspace Package
  DEPENDS:=+openvswitch-common
endef

define Package/openvswitch-switch/description
  openvswitch-switch provides the userspace components and utilities for the
  Open vSwitch kernel-based switch.
endef

define Package/openvswitch-brcompat
  $(call Package/openvswitch/Default)
  TITLE:=Open Flow Switch Userspace Package
  DPENDS:=+openvswitch-common
endef

define Package/openvswitch-brcompat/description
  openvswitch-brcompat provides a way for applications that use the Linux bridge
  to gradually migrate to Open vSwitch. Programs that ordinarily control the
  Linux bridge module, such as "brctl", instead control the Open vSwitch
  kernel-based switch.
endef

define KernelPackage/openvswitch
  SECTION:=kernel
  CATEGORY:=Kernel modules
  SUBMENU:=Network Support
  TITLE:=Open Flow Data Path Drivers
  KCONFIG:=CONFIG_BRIDGE
  DEPENDS:=+kmod-stp +kmod-ipv6
  FILES:= \
	$(PKG_BUILD_DIR)/datapath/linux/brcompat_mod.$(LINUX_KMOD_SUFFIX) \
	$(PKG_BUILD_DIR)/datapath/linux/openvswitch_mod.$(LINUX_KMOD_SUFFIX)
  AUTOLOAD:=$(call AutoLoad,21,openvswitch_mod brcompat_mod)
endef

define KernelPackage/openvswitch/description
  This package contains the Open vSwitch kernel moodule and bridge compat
  module. Furthermore, it supports OpenFlow.
endef

ifeq ($(KERNEL),2.6)
CONFIGURE_ARGS += --with-linux=$(LINUX_DIR) --with-rundir=/var/run
endif

define Build/Configure
	(cd $(PKG_BUILD_DIR); \
		autoreconf -v --install --force || exit 1 \
	);
	$(call Build/Configure/Default,$(CONFIGURE_ARGS))
endef

define Build/Compile
	$(MAKE) -C $(PKG_BUILD_DIR) \
		$(TARGET_CONFIGURE_OPTS) \
		CFLAGS="-I$(PKG_BUILD_DIR)/lib $(TARGET_CFLAGS) -std=gnu99" \
		LDFLAGS="-L$(PKG_BUILD_DIR)/lib $(TARGET_LDFLAGS)" \
		LDFLAGS_MODULES="$(TARGET_LDFLAGS) -L$(PKG_BUILD_DIR)/lib" \
		STAGING_DIR="$(STAGING_DIR)" \
		DESTDIR="$(PKG_INSTALL_DIR)/usr" \
		CROSS_COMPILE="$(TARGET_CROSS)" \
		ARCH="$(LINUX_KARCH)" \
		SUBDIRS="$(PKG_BUILD_DIR)/datapath/linux" \
		PATH="$(TARGET_PATH)" \
		KCC="$(KERNEL_CC)"
endef

define Package/openvswitch-ipsec/install
	$(INSTALL_DIR) $(1)/usr/sbin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/debian/ovs-monitor-ipsec $(1)/usr/sbin/
endef

define Package/openvswitch-common/install
	$(INSTALL_DIR) $(1)/sbin $(1)/etc/init.d
	$(INSTALL_BIN) ./files/etc/init.d/openvswitch.init $(1)/etc/init.d/openvswitch

	$(INSTALL_DIR) $(1)/sbin/
	$(INSTALL_BIN) ./files/sbin/ifdown $(1)/sbin/

	$(INSTALL_DIR) $(1)/lib/network/
	$(INSTALL_BIN) ./files/lib/network/config.sh $(1)/lib/network/

	$(INSTALL_DIR) $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utilities/ovs-appctl $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utilities/ovs-benchmark $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utilities/ovs-ofctl $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utilities/ovs-parse-leaks $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utilities/ovs-pki $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/ovsdb/ovsdb-client $(1)/usr/bin/
	$(INSTALL_DIR) $(1)/usr/sbin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utilities/bugtool/ovs-bugtool $(1)/usr/sbin/
endef

define Package/openvswitch-common/postinst
#!/bin/sh
[ -n "$${IPKG_INSTROOT}" ] || /etc/init.d/openvswitch enable || true
endef

define Package/openvswitch-controller/install
	$(INSTALL_DIR) $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utilities/ovs-controller $(1)/usr/bin/
endef

define Package/openvswitch-switch/install
	$(INSTALL_DIR) $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utilities/ovs-dpctl $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utilities/ovs-pcap $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utilities/ovs-tcpundump $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utilities/ovs-vlan-test $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/utilities/ovs-vsctl $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/ovsdb/ovsdb-tool $(1)/usr/bin/

	$(INSTALL_DIR) $(1)/usr/sbin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/vswitchd/ovs-vswitchd $(1)/usr/sbin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/ovsdb/ovsdb-server $(1)/usr/sbin/

	$(INSTALL_DIR) $(1)/usr/share/openvswitch/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/vswitchd/vswitch.ovsschema $(1)/usr/share/openvswitch/
endef

define Package/openvswitch-brcompat/install
	$(INSTALL_DIR) $(1)/usr/sbin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/vswitchd/ovs-brcompatd $(1)/usr/sbin/
endef

$(eval $(call BuildPackage,openvswitch-ipsec))
$(eval $(call BuildPackage,openvswitch-common))
$(eval $(call BuildPackage,openvswitch-controller))
$(eval $(call BuildPackage,openvswitch-switch))
$(eval $(call BuildPackage,openvswitch-brcompat))
$(eval $(call KernelPackage,openvswitch))

