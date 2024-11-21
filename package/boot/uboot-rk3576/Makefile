#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=uboot-rockchip
PKG_RELEASE:=2

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/ArmSoM/u-boot

PKG_SOURCE_DATE:=2024-04-18
PKG_SOURCE_VERSION:=57b8ceed1c31601ef81a5752a78c42e536c42cfe
PKG_MIRROR_HASH:=3ff3904f405e3389e316dfe0d54cc446563d98f610a81622f8bf41894f88a540

include $(INCLUDE_DIR)/u-boot.mk
include $(INCLUDE_DIR)/package.mk

define U-Boot/Default
  BUILD_TARGET:=rockchip
  UENV:=default
  HIDDEN:=1
endef

define U-Boot/armsom-sige5-rk3576
  BUILD_SUBTARGET:=armv8
  NAME:=ArmSoM Sige5
  BUILD_DEVICES:= \
    armsom_sige5
  DEPENDS:=+PACKAGE_u-boot-$(1):rkbin-rk3576
  ATF:=rk3576_bl31_v1.04.elf
  DDR:=rk3576_ddr_lp4_2112MHz_lp5_2736MHz_v1.03.bin
  TEE:=rk3576_bl32_v1.01.bin
  SOC:=rk3576
endef

UBOOT_TARGETS := \
  armsom-sige5-rk3576

UBOOT_CONFIGURE_VARS += USE_PRIVATE_LIBGCC=yes

UBOOT_MAKE_FLAGS += \
  IDB_SOC=$(SOC) TPL_BIN=$(STAGING_DIR_IMAGE)/$(DDR) u-boot.itb idbloader.img

define Build/Configure
	$(call Build/Configure/U-Boot)

	$(SED) 's/CONFIG_TOOLS_LIBCRYPTO=y/# CONFIG_TOOLS_LIBCRYPTO is not set/' $(PKG_BUILD_DIR)/.config
	$(SED) 's#CONFIG_MKIMAGE_DTC_PATH=.*#CONFIG_MKIMAGE_DTC_PATH="$(PKG_BUILD_DIR)/scripts/dtc/dtc"#g' $(PKG_BUILD_DIR)/.config
	echo 'CONFIG_IDENT_STRING=" OpenWrt"' >> $(PKG_BUILD_DIR)/.config
	$(CP) $(STAGING_DIR_IMAGE)/$(ATF) $(PKG_BUILD_DIR)/bl31.elf
ifneq ($(TEE),)
	$(CP) $(STAGING_DIR_IMAGE)/$(TEE) $(PKG_BUILD_DIR)/tee.bin
endif
endef

define Build/InstallDev
	$(INSTALL_DIR) $(STAGING_DIR_IMAGE)
	$(CP) $(PKG_BUILD_DIR)/idbloader.img $(STAGING_DIR_IMAGE)/$(BUILD_VARIANT)-idbloader.img
	$(CP) $(PKG_BUILD_DIR)/u-boot.itb $(STAGING_DIR_IMAGE)/$(BUILD_VARIANT)-u-boot.itb
endef

define Package/u-boot/install/default
endef

$(eval $(call BuildPackage/U-Boot))
