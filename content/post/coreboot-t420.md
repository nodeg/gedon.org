---
title: "Coreboot on the Lenovo T420"
date: 2019-12-09
draft: false
---

Since 2014 the Lenovo T420 is replacing my broken Dell XPS M1710. The first thing I did was replacing the HDD with a 256 GB Samsung 840 SSD and installing Arch Linux on it.
In the same year I stumbled across coreboot after gathering information related to the Snowden leaks the year before. Until October 2015 there was no support for the
T420 in coreboot and the [T420 wiki](https://www.coreboot.org/Board:lenovo/t420) page gave little information about the topic. Months have passed and finally in March 2017 I dealt with the subject again
and successfully flashed coreboot 4.5 on my T420, replacing the proprietary BIOS from Lenovo. The wiki page has now much more content and a guide on how to compile and build coreboot, too.

On 15th of October 2017 I built coreboot 4.6 and flashed the new image on my T420 and I am using this version until today. I did the following steps to flash coreboot. Most of the information are copied over from the corebook documentation on the T420.

The pinout of the Rasbperry Pi 3B is the following:
```bash
L                                                             CS
E                                                             |
F +--------------------------------------------------------------------------------------------------------+
T |    x    x    x    x    x    x    x    x    x    x    x    x    x    x    x    x    x    x    x    x    |
  |    x    x    x    x    x    x    x    x    x    x    x    x    x    x    x    x    x    x    x    x    |
E +--------------------------------------------^----^----^----^---------------------------------------^----+
D                                              |    |    |    |                                       |
G                                             3.3V  MOSI MISO |                                      GND
E                                             (VCC)          CLK
```
The pinout of the T420 is the following:

```bash
Screen     __
CS    1 --|  |-- 5  MOSI
MISO  2 --|  |-- 6  CLK
N/C   3 --|  |-- 7  N/C
GND   4 --|__|-- 8  VCC
```
My chip is a **MX25L6406E** so I have to provide flashrom with the `-c MX25L6406E/MX25L6408E` flag to access the chip.

Step 0 - Saving the old Lenovo BIOS
```bash
sudo flashrom -c MX25L6406E/MX25L6408E -p linux_spi:dev=/dev/spidev0.0,spispeed=512 -r bios_orig.bin
sudo flashrom -c MX25L6406E/MX25L6408E -p linux_spi:dev=/dev/spidev0.0,spispeed=512 -r bios_orig02.bin
md5sum bios_orig.bin bios_orig02.bin
```

Step 1 - Install tools and libraries needed for coreboot
```bash
sudo zypper in bison curl flashrom gcc-ada

```
Step 2 - Download coreboot source tree
```bash
git clone https://review.coreboot.org/coreboot ~/coreboot
cd coreboot
cd 3dparty/util/ifdtool
make
sudo make install
ifdtool -x ~/bios_orig.bin
mkdir -p ~/coreboot/3rdparty/blobs/mainboard/lenovo/t420
cd ~/coreboot/3rdparty/blobs/mainboard/lenovo/t420
mv ~/flashregion_0_flashdescriptor.bin descriptor.bin
mv ~/flashregion_2_intel_me.bin me.bin
mv ~/flashregion_3_gbe.bin gbe.bin
```

Step 3 - Build the coreboot toolchain
```bash
make crossgcc-i386 CPUS=$(nproc)
```

Step 4 - Build the payload - coreinfo
```bash
make -C payloads/coreinfo olddefconfig
make -C payloads/coreinfo
```

Step 5 - Configure the build
```bash
make menuconfig

# not a complete list
General Setup
    Use CMOS for configuration values

Mainboard
    Mainboard vendor >>> Select >> Lenovo
    Mainboard model >>> Select >>> T420

Chipset
    Add Intel descriptor.bin file
    Add Intel ME/TXE firmware
    Add gigabit ethernet configuration

Devices
    Enable PCIe Clock Power Management
    Enable PCIe ASPM L1 SubState

Generic Driver
    PS/2 keyboard init
```
Step 6 - build coreboot
```bash
make
```
The BIOS can be found in the folder build.

Step 7 - flash coreboot
```bash
# Saving the old bios again to check the cable connection before flashing
sudo flashrom -c MX25L6406E/MX25L6408E -p linux_spi:dev=/dev/spidev0.0,spispeed=512 -r bios01.bin
sudo flashrom -c MX25L6406E/MX25L6408E -p linux_spi:dev=/dev/spidev0.0,spispeed=512 -r bios02.bin
md5sum bios01.bin bios02.bin
sudo flashrom -c MX25L6406E/MX25L6408E -p linux_spi:dev=/dev/spidev0.0,spispeed=512 -w build/coreboot.rom
```

### Update coreboot internally without an external flashing device

When using openSUSE Tumbleweed you have to set `iomem=relaxed` as kernel parameter according to the flashrom FAQs.
```bash
sudo flashrom -c MX25L6406E/MX25L6408E -p internal:laptop=force_I_want_a_brick -w build/coreboot.rom
```
