#
# Copyright (C) 2006-2011 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=irssi
PKG_VERSION:=0.8.20
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=https://github.com/irssi/irssi/releases/download/0.8.20/
PKG_MD5SUM:=67d48c5feec2d3b949d088aa4abc3601

PKG_FIXUP:=autoreconf
PKG_INSTALL:=1

PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-$(BUILD_VARIANT)/$(PKG_NAME)-$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

define Package/irssi/Default
  SUBMENU:=Instant Messaging
  SECTION:=net
  CATEGORY:=Network
  DEPENDS:=+glib2 +libncurses +libpthread
  TITLE:=Console IRC client
  MAINTAINER:=Peter Wagner <tripolar@gmx.at>
  URL:=http://irssi.org/
endef

define Package/irssi/Default/description
	Irssi is a terminal based IRC client for UNIX systems.
endef

define Package/irssi
$(call Package/irssi/Default)
  TITLE+= (with OpenSSL support)
  DEPENDS+= +libopenssl
  VARIANT:=ssl
endef

define Package/irssi/description
$(call Package/irssi/Default/description)
 This package is built with OpenSSL support.
endef

define Package/irssi-nossl
$(call Package/irssi/Default)
  TITLE+= (without OpenSSL support)
  VARIANT:=nossl
endef

define Package/irssi-nossl/description
$(call Package/irssi/Default/description)
 This package is built without OpenSSL support.
endef

CONFIGURE_ARGS += \
	--with-perl=no \
	--with-glib-prefix="$(STAGING_DIR)/opt" \
	--with-ncurses="$(STAGING_DIR)/opt" \
	--with-textui \
	--with-bot \
	--disable-proxy

EXTRA_CFLAGS+=$(TARGET_CPPFLAGS)
EXTRA_LDFLAGS+=-lncurses

ifeq ($(BUILD_VARIANT),ssl)
	CONFIGURE_ARGS += \
		--with-ssl="$(STAGING_DIR)/opt"
endif

ifeq ($(BUILD_VARIANT),nossl)
	CONFIGURE_ARGS += \
		--without-ssl \
		--disable-ssl
endif

define Build/Configure
	(cd $(PKG_BUILD_DIR); \
	    ./autogen.sh; \
	);
	$(call Build/Configure/Default)
endef

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/include/irssi
	$(CP) $(PKG_INSTALL_DIR)/opt/include/irssi/* $(1)/opt/include/irssi/
endef

define Package/irssi/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/$(PKG_NAME) $(1)/opt/bin/$(PKG_NAME)
endef

Package/irssi-nossl/install = $(Package/irssi/install)

$(eval $(call BuildPackage,irssi))
$(eval $(call BuildPackage,irssi-nossl))
