# BLE Gateway Device Management (DM) Firmware Manifest Repo

This manifest repo will pull all the required firmware for the BLE Gateway DM Firmware.

> **NOTE:** Customization process is required to enable all features of Laird Connectivity provided Canvas Device Management platform. Please [contact Laird Connectivity](https://www.lairdconnect.com/contact) if you require Canvas Device Management Services before you start development.

## Cloning Firmware

> **WARNING:** On Windows do not clone into a starting folder path longer than 12 characters or else the firmware will not build.

This is a Zephyr `west` manifest repository. To learn more about `west` see [here](https://docs.zephyrproject.org/latest/guides/west/index.html).

To clone this repository properly use the `west` tool. To install `west` you will first need Python3.

Install `west` using `pip3`:

```
# Linux
pip3 install --user -U west

# macOS (Terminal) and Windows (cmd.exe)
pip3 install -U west
```

Once `west` is installed, clone this repository using `west init` and `west update`:

```
# Checkout the latest manifest on main
west init -m https://github.com/CanvasDM/ble_gateway_dm_firmware_manifest.git --mr main

# Checkout the latest manifest on GA1
west init -m https://github.com/CanvasDM/ble_gateway_dm_firmware_manifest.git --mr GA1

# Checkout a specific version
west init -m https://github.com/CanvasDM/ble_gateway_dm_firmware_manifest.git --mr v0.3.0

# Now, pull all the source described in the manifest
west update
```

## Preparing to Build

If this is your first time working with a Zephyr project on your computer you should follow the [Zephyr getting started guide](https://docs.zephyrproject.org/latest/getting_started/index.html#) to install all the tools.

v0.14.2 of the Zephyr SDK is recommended for building the firmware.

## Building the Firmware

> **WARNING:** Before building the firmware, be sure to install all python dependencies

From the directory where you ran `west update`, issue the following command:

```
# Linux, macOS and Windows
pip3 install --user -r zephyr/scripts/requirements.txt
pip3 install --user -r modules/lib/laird_connect/attributes/generator/requirements.txt
```

Then build the firmware

```
# For LwM2M telemetry connection

west build -p -b mg100 -d ble_gateway_dm_firmware/build/mg100 ble_gateway_dm_firmware -- -DAPP_TYPE=lwm2m

# For MQTT telemetry connection

west build -p -b mg100 -d ble_gateway_dm_firmware/build/mg100 ble_gateway_dm_firmware -- -DAPP_TYPE=mqtt
```

### Secure Sign Firmware

In order to build a secure image there are extra options that need to be used when building the firmware.

The following command will use test keys in the repo to show how to build a secure image.

```
# Linux, macOS

west build -p -b mg100 -d ble_gateway_dm_firmware/build/mg100 ble_gateway_dm_firmware -- -DAPP_TYPE=lwm2m \
-Db0_CONFIG_DISABLE_FLASH_PATCH=y \
-Dmcuboot_CONFIG_DISABLE_FLASH_PATCH=y \
-DCONFIG_LCZ_APPROTECT_STARTUP=y \
-DCONFIG_LCZ_APPROTECT_STARTUP_INIT_LEVEL=3 \
-DCONFIG_LCZ_APPROTECT_STARTUP_INIT_PRIORITY=2 \
-DCONFIG_SB_SIGNING_KEY_FILE=\"${PWD}/ble_gateway_dm_firmware/config/keys/debug_priv_a.pem\" \
-DCONFIG_SB_PUBLIC_KEY_FILES=\"${PWD}/ble_gateway_dm_firmware/config/keys/debug_pub_b.pem,config/keys/debug_pub_c.pem\" \
-Dmcuboot_CONFIG_BOOT_SIGNATURE_TYPE_ECDSA_P256=y \
-Dmcuboot_CONFIG_BOOT_SIGNATURE_KEY_FILE=\\\"${PWD}/ble_gateway_dm_firmware/config/keys/debug_priv_c.pem\\\"

# Windows

west build -p -b mg100 -d ble_gateway_dm_firmware/build/mg100 ble_gateway_dm_firmware -- -DAPP_TYPE=lwm2m \
-Db0_CONFIG_DISABLE_FLASH_PATCH=y \
-Dmcuboot_CONFIG_DISABLE_FLASH_PATCH=y \
-DCONFIG_LCZ_APPROTECT_STARTUP=y \
-DCONFIG_LCZ_APPROTECT_STARTUP_INIT_LEVEL=3 \
-DCONFIG_LCZ_APPROTECT_STARTUP_INIT_PRIORITY=2 \
-DCONFIG_SB_SIGNING_KEY_FILE=\"%CD%/ble_gateway_dm_firmware/config/keys/debug_priv_a.pem\" \
-DCONFIG_SB_PUBLIC_KEY_FILES=\"%CD%/ble_gateway_dm_firmware/config/keys/debug_pub_b.pem,config/keys/debug_pub_c.pem\" \
-Dmcuboot_CONFIG_BOOT_SIGNATURE_TYPE_ECDSA_P256=y \
-Dmcuboot_CONFIG_BOOT_SIGNATURE_KEY_FILE=\\\"%CD%/ble_gateway_dm_firmware/config/keys/debug_priv_c.pem\\\"
```
