#
# Copyright (C) 2015 Thomas Weißschuh
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=esniper
PKG_VERSION:=2.31.0
PKG_RELEASE:=1
PKG_LICENSE:=BSD-2-Clause
PKG_LICENSE_FILES:=COPYING
PKG_MAINTAINER:=Thomas Weißschuh <openwrt@t-8ch.de>

VERSION_TRANSFORMED:=$(subst .,-,$(PKG_VERSION))

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(VERSION_TRANSFORMED)
PKG_SOURCE:=$(PKG_NAME)-$(VERSION_TRANSFORMED).tgz
PKG_SOURCE_URL:=@SF/$(PKG_NAME)
PKG_MD5SUM:=4b411588c4a223acef7333ab684c7618

PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define Package/esniper
  SECTION:=net
  CATEGORY:=Network
  TITLE:=Simple, lightweight tool for sniping eBay auctions
  URL:=http://esniper.sourceforge.net/
  DEPENDS:=+libcurl +ca-certificates
endef

define Package/esniper/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/esniper $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_BUILD_DIR)/frontends/snipe $(1)/opt/bin/
endef

$(eval $(call BuildPackage,esniper))
