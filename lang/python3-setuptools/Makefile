#
# Copyright (C) 2014-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=python3-setuptools
PKG_VERSION:=18.5
PKG_RELEASE:=1

PKG_SOURCE:=setuptools-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://pypi.python.org/packages/source/s/setuptools/
PKG_MD5SUM:=533c868f01169a3085177dffe5e768bb

PKG_LICENSE:=PSF

PKG_BUILD_DIR:=$(BUILD_DIR)/setuptools-$(PKG_VERSION)

include $(INCLUDE_DIR)/package.mk
$(call include_mk, python3-package.mk)

define Package/python3-setuptools
	SUBMENU:=Python
	SECTION:=lang
	CATEGORY:=Languages
	TITLE:=Tool for installing Python $(PYTHON3_VERSION) packages.
	URL:=https://bitbucket.org/pypa/setuptools
	DEPENDS:=+python3 +python3-dev +python3-lib2to3
endef

define Package/python3-setuptools/description
	Easily download, build, install, upgrade, and uninstall Python $(PYTHON3_VERSION) packages
endef

define Build/Compile
	$(call Build/Compile/Py3Mod,,\
		install --prefix="$(PKG_INSTALL_DIR)/usr" \
	)
endef

define Build/InstallDev
	$(INSTALL_DIR) $(PYTHON3_LIB_DIR)
	$(CP) \
		$(PKG_INSTALL_DIR)$(PYTHON3_PKG_DIR)/* \
		$(PYTHON3_LIB_DIR)
endef

# If python3-pip was installed first, move conflicting files before installing
define Package/python3-setuptools/preinst
#!/bin/sh
cd "$${IPKG_INSTROOT}$(PYTHON3_PKG_DIR)"
[ -f easy-install.pth ] && mv -f easy-install.pth easy-install.pth.pip
[ -f site.py ] && mv -f site.py site.py.pip
exit 0
endef

# Move them back after installing
define Package/python3-setuptools/postinst
#!/bin/sh
cd "$${IPKG_INSTROOT}$(PYTHON3_PKG_DIR)"
[ -f easy-install.pth.pip ] && {
	mv -f easy-install.pth easy-install.pth.setuptools
	mv -f easy-install.pth.pip easy-install.pth
}
[ -f site.py.pip ] && {
	mv -f site.py site.py.setuptools
	mv -f site.py.pip site.py
}
exit 0
endef

# If python3-setuptools gets removed first, backup pip's files,
# move setuptools' in place
define Package/python3-setuptools/prerm
#!/bin/sh
cd "$${IPKG_INSTROOT}$(PYTHON3_PKG_DIR)"
[ -f easy-install.pth.setuptools ] && [ -f easy-install.pth ] && {
	mv -f easy-install.pth easy-install.pth.pip
	mv -f easy-install.pth.setuptools easy-install.pth
}
[ -f site.py.setuptools ] && [ -f site.py ] && {
	mv -f site.py site.py.pip
	mv -f site.py.setuptools site.py
}
exit 0
endef

# Restore pip's files after setuptools' removal
define Package/python3-setuptools/postrm
#!/bin/sh
cd "$${IPKG_INSTROOT}$(PYTHON3_PKG_DIR)"
[ -f easy-install.pth.pip ] && mv -f easy-install.pth.pip easy-install.pth
[ -f site.py.pip ] && mv -f site.py.pip site.py
exit 0
endef

define Py3Package/python3-setuptools/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/easy_install-$(PYTHON3_VERSION) $(1)/usr/bin/
	$(LN) easy_install-$(PYTHON3_VERSION) $(1)/usr/bin/easy_install3
endef

$(eval $(call Py3Package,python3-setuptools))
$(eval $(call BuildPackage,python3-setuptools))
