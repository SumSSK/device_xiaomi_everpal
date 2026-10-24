# Xiaomi Everpal Device Tree for LineageOS 23.2

This repository contains the device configuration for the Xiaomi device family identified by the board names `evergo`, `evergreen`, `everpal`, and `opal`. It is structured as a LineageOS 23.2 (Android 14) device tree and includes the required files for building a custom ROM on compatible hardware based on the MediaTek MT6833 platform.

This project is intended for developers who want to compile and flash a LineageOS-based build on the target device. It defines the board configuration, init scripts, product configuration, overlays, permissions, and SELinux policy required for the device to boot and run with AOSP/LineageOS.

## Device Summary

- Device family: Xiaomi
- Codename: `everpal` (also aliases: `evergo`, `evergreen`, `opal`)
- Platform: MediaTek MT6833
- Android version target: LineageOS 23.2 (Android 14)
- Architecture: ARM64
- CPU: ARMv8-A 64-bit with Cortex-A76 / Cortex-A55
- Screen density: 440 dpi
- Dynamic partitions: Enabled
- Verified boot: Enabled
- Recovery: Uses `TARGET_RECOVERY_FSTAB := init/fstab.mt6833`

## Repository Purpose

This repository is a device tree and not a full operating system or vendor package. It is designed to be used alongside:

- A compatible LineageOS source tree
- A matching proprietary vendor blob package from Xiaomi
- The corresponding kernel source for the MT6833 platform

The repo contains the device-level customizations needed for Android compatibility, including:

- Product and board definitions
- Hardware overlays
- Audio, Wi-Fi, sensors, and power configuration
- Init scripts and fstab entries
- SELinux policy rules
- Vendor blob integration support

## Repository Structure

```text
.
├── Android.bp
├── AndroidProducts.mk
├── BoardConfig.mk
├── board-info.txt
├── device.mk
├── extract-files.py
├── lineage_everpal.mk
├── proprietary-files.txt
├── setup-makefiles.py
├── configs/
│   ├── audio/
│   ├── keylayout/
│   ├── media/
│   ├── permissions/
│   ├── props/
│   ├── sensors/
│   ├── vintf/
│   ├── wifi/
│   ├── powerhint.json
│   ├── public.libraries.txt
│   ├── task_profiles.json
│   └── thermal_info_config.json
├── init/
│   ├── Android.bp
│   ├── fstab.mt6833
│   ├── init.batterysecret.rc
│   ├── init.connectivity.rc
│   ├── init.modem.rc
│   ├── init.mt6833.power.rc
│   ├── init.mt6833.rc
│   ├── init.mt6833.thermal.rc
│   ├── init.project.rc
│   ├── init.recovery.mt6833.rc
│   ├── init.sensor_2_0.rc
│   ├── init.stnfc.rc
│   └── ueventd.mt6833.rc
├── overlay/
│   ├── Frameworks/
│   ├── Launcher3/
│   ├── Settings/
│   ├── SystemUI/
│   ├── Tethering/
│   └── WiFi/
├── sepolicy/
│   ├── private/
│   └── vendor/
└── README.md
```

## Key Files

### `BoardConfig.mk`
This file defines the build board configuration, including:

- target architecture (`arm64`)
- boot image configuration
- partition layout
- MediaTek platform selection (`mt6833`)
- AVB / verified boot setup
- dynamic partitioning
- SELinux policy inclusion
- Wi-Fi and recovery settings

### `device.mk`
This file is the main product configuration and includes:

- system and vendor product inheritance
- hardware packages for audio, camera, sensors, Wi-Fi, USB, power, and thermal
- overlays for framework and UI customization
- permissions and property files
- A/B OTA setup
- MediaTek-specific service integrations

### `lineage_everpal.mk`
This is the product definition for the LineageOS build, including:

- `PRODUCT_NAME := lineage_everpal`
- `PRODUCT_DEVICE := everpal`
- `PRODUCT_MANUFACTURER := Xiaomi`
- `PRODUCT_BRAND := Redmi`
- `PRODUCT_MODEL := everpal`

### `init/`
Contains the device-specific init and filesystem configuration for:

- boot-time services
- modem and connectivity setup
- battery handling
- thermal behavior
- recovery environment
- sensor drivers and uevent rules

### `configs/`
Contains important runtime configuration for:

- audio policies and effects
- media codecs
- keypad layouts
- permissions and app policy
- power management hints
- thermal behavior
- Wi-Fi and vendor properties
- VINTF manifest configuration

### `overlay/`
Provides UI and framework level custom overlays for:

- system framework behavior
- launcher behavior
- settings UI customization
- SystemUI visuals
- tethering preferences
- Wi-Fi related configuration

### `sepolicy/`
Adds device-specific SELinux rules for MediaTek and Xiaomi components, including vendor services and system behavior.

## Build Requirements

To build this device tree properly, you need a working Android/LineageOS build environment. Typical prerequisites include:

- Ubuntu 22.04 or a compatible Linux environment
- Git
- Python 3
- OpenJDK 17 or equivalent
- Build-essential packages
- Repo tool installed and configured
- Compatible kernel source for `mt6833`
- Proprietary vendor files from Xiaomi for the target device

A typical environment setup includes installing packages such as:

```bash
sudo apt update
sudo apt install git python3 python3-pip openjdk-17-jdk build-essential zip curl
```

See the official LineageOS build documentation for the most current package list for your host OS.

## Source Setup

Create a working directory and initialize the LineageOS source tree:

```bash
mkdir lineageos
cd lineageos
repo init -u https://github.com/LineageOS/android.git -b lineage-23.2
```

Then add this repository as the device tree for the target device. You can place it in:

```text
device/xiaomi/everpal/
```

If using a local manifest, make sure the XML points to this repo:

```xml
<project name="Pavan-M-S/device_xiaomi_everpal" path="device/xiaomi/everpal" remote="github" />
```

Then sync the source:

```bash
repo sync -c --force-sync --no-clone-bundle --no-tags
```

## Required Vendor Files

This device tree depends on proprietary files from Xiaomi. You must place the vendor blobs in the proper AOSP/LineageOS vendor path, typically:

```text
vendor/xiaomi/everpal/
```

The repository references:

- `vendor/xiaomi/everpal/everpal-vendor.mk`
- `vendor/xiaomi/everpal/BoardConfigVendor.mk`

Without the correct vendor tree, the build will fail during compilation or runtime configuration.

## Build Instructions

After the source tree and vendor files are in place:

```bash
source build/envsetup.sh
lunch lineage_everpal-userdebug
mka bacon
```

Or, if you prefer to build a system image only:

```bash
source build/envsetup.sh
lunch lineage_everpal-userdebug
mka systemimage
```

You may need to clean the build tree if switching between builds or vendor trees:

```bash
make clobber
```

## Flashing

After successful build, flash the generated images to the device using fastboot or your preferred recovery method. Typical output artifacts are generated in:

```text
out/target/product/everpal/
```

Common images include:

- `boot.img`
- `system.img`
- `vendor.img`
- `product.img`
- `recovery.img`
- `vbmeta.img`

Use a bootloader-compatible flashing method for your device and always ensure you have the required device-specific instructions before flashing.

## Notes and Compatibility

- This repo is tuned for the `mt6833` chip family and uses a Xiaomi-based vendor tree.
- Many device-specific values are defined in `BoardConfig.mk` and `device.mk`.
- The device supports A/B OTA features and dynamic partitions.
- The repo includes files for audio, thermal, power, Wi-Fi, NFC, and init-level support.
- The configuration is intended to match the LineageOS 23.2 build system and Android 14 platform requirements.

## Troubleshooting

If you run into build issues, check the following:

1. Ensure the vendor blobs match the exact device variant.
2. Confirm the kernel source matches the MT6833 platform and expected defconfig.
3. Verify the local manifest path and repo sync state.
4. Ensure your environment uses the correct Java and build packages.
5. Rebuild after cleaning stale configuration if switching between branches or device trees.

Common errors usually come from missing proprietary binaries, incorrect board name mapping, or a mismatched kernel source tree.

## Credits

This repository is based on the standard Android device tree structure used for LineageOS devices and is adapted for the Xiaomi `everpal` platform.

Thanks to the wider Android custom ROM community, LineageOS contributors, Xiaomi device maintainers, and upstream AOSP/LineageOS developers for the underlying work that makes device-specific builds possible.

## Disclaimer

This is a custom device tree for development and testing. Use it responsibly and ensure you have the legal right to use the required proprietary Xiaomi files and source code. Building and flashing custom ROMs may void warranties or affect device functionality if used incorrectly.

## License

This repository includes SPDX license headers consistent with Android and LineageOS device trees. Please check the source files for exact licensing details where applicable.

---

If you want, I can also create a more polished version specifically tailored for GitHub presentation, including badges, a screenshot section, and a concise contributor/build workflow section.
