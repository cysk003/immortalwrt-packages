# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NPM_NAME:=lwm2m-node-lib
PKG_NAME:=node-$(PKG_NPM_NAME)
PKG_VERSION:=1.4.0
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NPM_NAME)-$(PKG_VERSION).tgz
PKG_SOURCE_URL:=https://mirrors.tencent.com/npm/$(PKG_NPM_NAME)/-/ \
                https://registry.npmjs.org/$(PKG_NPM_NAME)/-/
PKG_HASH:=ea73956ecfd07b5d44c453f06ec5ffc90348e6c2a4f52dc3f64fdfb0ee538f20

PKG_MAINTAINER:=Hirokazu MORIKAWA <morikw2@gmail.com>
PKG_LICENSE:=AGPL-3.0
PKG_LICENSE_FILES:=

PKG_BUILD_DEPENDS:=node/host
PKG_BUILD_PARALLEL:=1
PKG_BUILD_FLAGS:=no-mips16

include $(INCLUDE_DIR)/package.mk

define Package/node-lwm2m-node-lib
  SUBMENU:=Node.js
  SECTION:=lang
  CATEGORY:=Languages
  TITLE:=Library for building OMA Lightweight M2M
  URL:=https://www.npmjs.org/package/lwm2m-node-lib
  DEPENDS:=+node
endef

define Package/node-lwm2m-node-lib/description
 The Open Mobile Alliance Lightweight M2M protocol is a machine to machine communication protocol built over COAP, and meant to communicate resource constrained devices. The protocol defines two roles for the devices: a Lightweight M2M Client (the constrained device) and a Lightweight M2M Server (meant to consume the device data and control its execution).
endef

TAR_OPTIONS+= --strip-components 1
TAR_CMD=$(HOST_TAR) -C $(1) $(TAR_OPTIONS)

NODEJS_CPU:=$(subst powerpc,ppc,$(subst aarch64,arm64,$(subst x86_64,x64,$(subst i386,ia32,$(ARCH)))))
TMPNPM:=$(shell mktemp -u XXXXXXXXXX)
NPM_CACHE_DIR:=$(if $(call qstrip,$(CONFIG_NODEJS_NPM_CACHE_DIR)),$(call qstrip,$(CONFIG_NODEJS_NPM_CACHE_DIR)),$(TMP_DIR))

TARGET_CFLAGS+=$(FPIC)
TARGET_CPPFLAGS+=$(FPIC)

define Build/Compile
	cd $(PKG_BUILD_DIR); \
	$(MAKE_VARS) \
	$(MAKE_FLAGS) \
	npm_config_nodedir=$(STAGING_DIR)/usr/ \
	$(if $(CONFIG_NODEJS_NPM_KEEP_CACHE), npm_config_cache=$(NPM_CACHE_DIR)/npm-cache-$(PKG_NPM_NAME),npm_config_cache=$(TMP_DIR)/npm-cache-$(TMPNPM)) \
	npm install --prefer-offline --no-audit --global-style --install-strategy=shallow --no-save --omit=dev --no-package-lock
	rm -rf $(TMP_DIR)/npm-cache-$(TMPNPM)
	rm -f $(PKG_BUILD_DIR)/node_modules/.package-lock.json
	find $(PKG_BUILD_DIR)/node_modules -type d -empty -print0 | xargs -0 -r rmdir || true
endef

define Package/node-lwm2m-node-lib/install
	$(INSTALL_DIR) $(1)/usr/lib/node/$(PKG_NPM_NAME)
	$(CP) $(PKG_BUILD_DIR)/{package.json,*.md,*.js} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(CP) $(PKG_BUILD_DIR)/{CHANGES_NEXT_RELEASE,*.txt} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(CP) $(PKG_BUILD_DIR)/{node_modules,lib} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(CP) $(PKG_BUILD_DIR)/{bin,docs,test} \
		$(1)/usr/lib/node/$(PKG_NPM_NAME)/
	$(INSTALL_DIR) $(1)/usr/bin
	$(LN) ../lib/node/$(PKG_NPM_NAME)/bin/iotagent-lwm2m-client.js $(1)/usr/bin/iotagent-lwm2m-client
	$(LN) ../lib/node/$(PKG_NPM_NAME)/bin/iotagent-lwm2m-server.js $(1)/usr/bin/iotagent-lwm2m-server
	$(INSTALL_DIR) $(1)/usr/lib/node_modules
	$(LN) ../node/$(PKG_NPM_NAME) $(1)/usr/lib/node_modules/$(PKG_NPM_NAME)
endef

define Package/node-lwm2m-node-lib/postrm
#!/bin/sh
rm /usr/lib/node_modules/lwm2m-node-lib || true
rm -rf /usr/lib/node/lwm2m-node-lib || true
endef

$(eval $(call BuildPackage,node-lwm2m-node-lib))
