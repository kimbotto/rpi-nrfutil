# nRF Util for Raspberry Pi

[![rpi-nrfutil Actions Status](https://github.com/LuDuda/rpi-nrfutil/workflows/rpi-nrfutil/badge.svg)](https://github.com/LuDuda/rpi-nrfutil/actions)

## Overview

This repository contains a set of scripts presenting how to build [nRF Util](https://github.com/NordicSemiconductor/pc-nrfutil) executable for Raspberry Pi (armv7l architecture).

Project uses QEMU based emulated environment for the Raspberry Pi, and can be run on Linux system (tested on Ubuntu 18.04).

The resulted `nrfutil` was verified for:
 - generating firmware packages
 - flashing [nRF52840 Dongle (PCA10059)](https://www.nordicsemi.com/Software-and-Tools/Development-Kits/nRF52840-Dongle) using serial DFU over USB
 - triggering bootloader mode using USB DFU trigger library

This is experimental project and other functionalities were not tested.

## Installing nRF Util from Raspberry Pi

The easiest way to fetch `nrfutil` is to download it from [GitHub release assets](https://github.com/LuDuda/rpi-nrfutil/releases/download/v6.1.0/nrfutil) or from the [GitHub actions](https://github.com/LuDuda/rpi-nrfutil/actions)

For example:

```bash
wget https://github.com/LuDuda/rpi-nrfutil/releases/download/v6.1.0/nrfutil
chmod +x nrfutil
nrfutil --help
```

## Building nRF Util on Linux

Run the following commands to prepare the build environment and start the building process.

```bash
./script/bootstrap
./script/build
```

After a successful build, the `nrfutil` executable can be found in the `output` folder.

#### nRF Util version

This project supports `nrfutil` in version 6.1.0. To change it's version, adjust the content of `script/_nrfutil` script.

#### Building nRF Util on Raspberry Pi

Alternatively, you can also install/build the `nrfutil` directly on your Raspberry Pi by following the `script/_nrfutil` script.

For older versions of Rasbian such as Stretch, you would need also to build Python3.7 from sources with development support.


```bash
sudo apt-get update -y
sudo apt-get install build-essential tk-dev libncurses5-dev libncursesw5-dev libreadline6-dev libdb5.3-dev libgdbm-dev libsqlite3-dev libssl-dev libbz2-dev libexpat1-dev liblzma-dev zlib1g-dev libffi-dev -y
wget https://www.python.org/ftp/python/3.7.4/Python-3.7.4.tar.xz
tar xf Python-3.7.4.tar.xz
cd Python-3.7.4
./configure --enable-shared
make
sudo make altinstall
```

## Flashing nRF52840 Dongle from Raspberry Pi

The [nRF52840 Dongle (PCA10059)](https://www.nordicsemi.com/Software-and-Tools/Development-Kits/nRF52840-Dongle) incorporates Nordic’s bootloader from Nordic’s nRF5 SDK. 
`nrfutil` supports generating and flashing firmware packages over USB.

To update the firmware, perform the following steps:

1. Enter the Bootloader mode on the board.

   To do that, either push the reset button on the board or make use of DFU USB software trigger. The second option requires additional support in the existing application firmware.

2. Generate firmware package.

   ```bash
   nrfutil pkg generate --hw-version 52 --sd-req=0x00 \
     --application app.hex --application-version 1 app.zip
   ```

3. Flash new firmware.

   ```bash
   nrfutil dfu usb-serial -pkg app.zip -p /dev/ttyACM0
   ```
