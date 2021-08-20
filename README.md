# Q8
![Alt text](imgs/main.jpg)
  
## Introduction
Q8 handheld is a very cheap device and the LCD screen sucks, just like shit. It is better for studying embedded system rather than play some games on this device. Now, I have ported Linux system (almost same as Miyoo) into this cheap device. If you are looking for Linux OS running on this device, here you can find all of resources you want. But the LCD model might be different with your Q8 device, so, I can't make sure whether it can run on your device or not. Anyway, follow the below steps to build a Linux system for your Q8 device, enjoy !  
  
|Component|Description                                     |
|---------|------------------------------------------------|
|CPU      |GB1 ZH1901MP02C (Allwinner F1C100S)             |
|RAM      |32MB                                            |
|Storage  |8MB                                             |
|Screen   |2.8" 320x240                                    |
|Slot     |MicroSD                                         |
|Gamepad  |DPad, 4 Buttons, Start, Select, VOL, L, R, Power|
|USB      |Client                                          |
|Battery  |3.7V 1100mA                                     |
|Dimension|120mm x 80mm x 18mm                             |
|Weight   |224g                                            |
  
## How to build Linux OS for Q8  
### prepare environment
-  Debian 10 (x64)
-  download all of sources in release page
  
### configure toolchain
-  extract toolchain.7z into /opt/miyoo
-  export command
   -  $ export PATH=$PATH:/opt/miyoo/bin
  
### build uboot
-  $ ARCH=arm CROSS_COMPILE=arm-linux- make q8_sdcard_defconfig && ARCH=arm CROSS_COMPILE=arm-linux- make
  
### build kernel
-  $ ARCH=arm CROSS_COMPILE=arm-linux- make q8_defconfig && ARCH=arm CROSS_COMPILE=arm-linux- make zImage modules dtbs
  
### build mininit
-  $ ARCH=arm CROSS_COMPILE=arm-linux- make
  
### build boot.scr
-  $ mkimage -C none -A arm -T script -d boot.cmd boot.scr
  
### build rootfs
-  download buildroot-2018.02.9 from https://buildroot.org
-  use config_buildroot-2018.02.9(in devel.zip) and then make it
  
### prepare sdcard
-  partition 1: 512MB FAT32 (boot.scr, suniv-f1c500s-miyoo.dtb, zImage, mininit, rootfs, dev, root)
-  partition 2: FAT32 (GMenu2X and emulators)
  
### flash uboot:
-  $ sudo dd if=u-boot-sunxi-with-spl.bin of=/dev/sdX bs=1024 seek=8
  
### copy files
-  copy boot.scr into partition 1
-  copy zImage into partition 1
-  copy suniv-f1c500s-miyoo.dtb into partition 1
-  copy mininit (original name: mininit-syspart) into partition 1
-  copy rootfs (squashfs) into partition 1
-  create empty folder "dev" into partition 1
-  create empty folder "root" into partition 1
-  copy GMenu2X and emulators into partition 2
  
### https://steward-fu.github.io/website/index.htm
