#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/uclibc++.mk

PKG_NAME:=mysql
PKG_VERSION:=5.1.73
PKG_RELEASE:=2

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=\
	http://mysql.he.net/Downloads/MySQL-5.1/ \
	http://www.linorg.usp.br/mysql/Downloads/MySQL-5.1/ \
	http://www.mirrorservice.org/sites/ftp.mysql.com/Downloads/MySQL-5.1/ \
	http://mirror.switch.ch/ftp/mirror/mysql/Downloads/MySQL-5.1/

PKG_MD5SUM:=887f869bcc757957067b9198f707f32f
PKG_MAINTAINER:=Jo-Philipp Wich <jo@mein.io>
PKG_LICENSE:=GPL-2.0

PKG_BUILD_DEPENDS:=libncurses libreadline
PKG_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0

PKG_FIXUP:=libtool

include $(INCLUDE_DIR)/package.mk

define Package/libmysqlclient/Default
  SECTION:=libs
  CATEGORY:=Libraries
  DEPENDS:=$(CXX_DEPENDS) +zlib
  TITLE:=MySQL client library
  URL:=http://dev.mysql.com/
endef

define Package/mysql-server
  SECTION:=utils
  CATEGORY:=Utilities
  DEPENDS:=+libmysqlclient +libpthread +libncursesw +libreadline
  TITLE:=MySQL Server
  URL:=http://dev.mysql.com/
  SUBMENU:=database
endef

define Package/libmysqlclient
  $(call Package/libmysqlclient/Default)
endef

define Package/libmysqlclient-r
  $(call Package/libmysqlclient/Default)
  TITLE += threadsafe
  DEPENDS+= +libpthread
endef


TARGET_CFLAGS += $(FPIC)

CONFIGURE_ARGS += \
	--enable-shared \
	--enable-static \
	--enable-thread-safe-client \
	--disable-assembler \
	--with-pthread \
	--with-server \
	--without-mysqlmanager \
	--with-mysqld-user=root \
	--with-unix-socket-path=/opt/tmp \
	--without-libwrap \
	--with-low-memory \
	--without-embedded-server \
	--without-query-cache \
	--without-ssl \
	--without-docs \
	--without-man \
	--with-readline \
	--without-debug \
	--with-named-thread-libs=-lpthread

CONFIGURE_VARS += \
	mysql_cv_compress=yes \
	mysql_cv_gethostname_style=glibc2 \
	mysql_cv_gcc_atomic_builtins=yes \
	mysql_cv_gcc_atomic_builtins_pthread_t=yes \
	ac_cv_c_stack_direction=-1

define Build/Compile
	+$(MAKE) $(PKG_JOBS) -C "$(PKG_BUILD_DIR)" \
		SUBDIRS="include" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		all
	$(MAKE) -C "$(PKG_BUILD_DIR)" \
		SUBDIRS="include" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		install
	g++ -I $(PKG_BUILD_DIR)/include \
		-o $(PKG_BUILD_DIR)/sql/gen_lex_hash \
		$(PKG_BUILD_DIR)/sql/gen_lex_hash.cc
	+$(MAKE) $(PKG_JOBS) -C "$(PKG_BUILD_DIR)/libmysql" \
		CC="g++" \
		CFLAGS="$(HOST_CFLAGS)" \
		CPPFLAGS="$(HOST_CFLAGS)" \
		LDFLAGS="$(HOST_LDFLAGS)" \
		conf_to_src
	+$(MAKE) $(PKG_JOBS) -C "$(PKG_BUILD_DIR)" \
		SUBDIRS="libmysql" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		all
	$(MAKE) -C "$(PKG_BUILD_DIR)" \
		SUBDIRS="libmysql" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		install
	+$(MAKE) $(PKG_JOBS) -C "$(PKG_BUILD_DIR)" \
		SUBDIRS="libmysql_r" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		all
	$(MAKE) -C "$(PKG_BUILD_DIR)" \
		SUBDIRS="libmysql_r" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		install
	$(MAKE) -C "$(PKG_BUILD_DIR)" \
		SUBDIRS="scripts" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		bin_SCRIPTS="mysql_config" \
		install
	+$(MAKE) $(PKG_JOBS) -C "$(PKG_BUILD_DIR)" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		all
	$(MAKE) -C "$(PKG_BUILD_DIR)" \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		install
endef


define Build/InstallDev
	$(INSTALL_DIR) $(2)/bin $(1)/opt/bin $(1)/opt/include $(1)/opt/lib
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/mysql_config $(1)/opt/bin/
	ln -sf $(STAGING_DIR)/opt/bin/mysql_config $(2)/bin/
	$(CP) $(PKG_INSTALL_DIR)/opt/include/mysql $(1)/opt/include/
	# NOTE: needed for MySQL-Python
	$(CP) $(PKG_BUILD_DIR)/include/mysqld_error.h $(1)/opt/include/mysql/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/mysql $(1)/opt/lib/
	rm -f $(1)/opt/lib/mysql/libmysqlclient.la
endef

define Package/libmysqlclient/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/mysql/libmysqlclient.so.* $(1)/opt/lib/
endef
define Package/libmysqlclient-r/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/mysql/libmysqlclient_r.so.* $(1)/opt/lib/
endef

define Package/mysql-server/install
	$(INSTALL_DIR) $(1)/opt/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/mysql $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/lib/mysqld $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/myisamchk $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/mysqladmin $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/mysqldump $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/mysql_install_db $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/my_print_defaults $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/mysql_upgrade $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/resolveip $(1)/opt/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/mysqlcheck $(1)/opt/bin/
	$(INSTALL_DIR) $(1)/opt/etc/init.d/
	$(INSTALL_BIN) ./files/S70mysqld $(1)/opt/etc/init.d
	$(INSTALL_CONF) conf/my.cnf $(1)/opt/etc/
	$(INSTALL_DIR) $(1)/opt/share/mysql
	$(INSTALL_DIR) $(1)/opt/share/mysql/english
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/share/mysql/english/errmsg.sys $(1)/opt/share/mysql/english
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/share/mysql/fill_help_tables.sql $(1)/opt/share/mysql/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/share/mysql/mysql_system_tables.sql $(1)/opt/share/mysql/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/share/mysql/mysql_system_tables_data.sql $(1)/opt/share/mysql/
	$(INSTALL_DIR) $(1)/opt/share/mysql/charsets
	$(CP) $(PKG_INSTALL_DIR)/opt/share/mysql/charsets/* $(1)/opt/share/mysql/charsets/
endef

define Package/mysql-server/conffiles
/opt/etc/my.cnf
endef

$(eval $(call BuildPackage,mysql-server))
$(eval $(call BuildPackage,libmysqlclient))
$(eval $(call BuildPackage,libmysqlclient-r))
