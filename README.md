# Q8
![Alt text](imgs/main.jpg)
  
## Introduction
Q8 handheld is a very cheap device and the LCD screen is very bad quality. It is better for studying embedded system rather than play some games on this device. Now, I have ported Linux system (almost same as Miyoo) into this cheap device. If you are looking for Linux OS running on this device, here you can find all of resources you want. But the LCD have many variants (different model), so, I can only make sure it runs on my Q8 device well, maybe, not for your device. Anyway, follow the below steps to build a Linux system for your Q8 device, enjoy !
  
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
  
## How to build Linux OS for Miyoo(Bittboy)  
### prepare environment
-  Debian 9 (x64)
-  download all of sources in release page
  
### configure toolchain
-  extract toolchain.7z into /opt/miyoo
-  export command
   -  export PATH=$PATH:/opt/miyoo/bin
  
### build uboot
-  boot from spi flash
   -  ARCH=arm CROSS_COMPILE=arm-linux- make licheepi_nano_spiflash_defconfig && ARCH=arm CROSS_COMPILE=arm-linux- make
-  boot from sdcard
   -  ARCH=arm CROSS_COMPILE=arm-linux- make licheepi_nano_defconfig && ARCH=arm CROSS_COMPILE=arm-linux- make
  
### build kernel (sdcard 4bits)
-  vim arch/arm/boot/dts/suniv-f1c500s-miyoo.dts +55
   -  bus-width = <4>;
-  ARCH=arm CROSS_COMPILE=arm-linux- make miyoo_defconfig && ARCH=arm CROSS_COMPILE=arm-linux- make zImage modules dtbs
  
### build kernel (sdcard 1bit)
-  vim arch/arm/boot/dts/suniv-f1c500s-miyoo.dts +55
   -  bus-width = <1>;
-  ARCH=arm CROSS_COMPILE=arm-linux- make miyoo_defconfig && ARCH=arm CROSS_COMPILE=arm-linux- make zImage modules dtbs
  
### build boot.scr
-  mkimage -C none -A arm -T script -d boot.cmd boot.scr
  
### prepare sdcard (>= 4GB)
-  partition 1: 32MB FAT32 (boot.scr, dtb and zImage)
-  partition 2: 256MB EXT4 (rootfs)
-  partition 3: 256MB SWAP
-  partition 4: FAT32 (GMenu2X, config files and emulators)
  
### flash uboot:
-  boot from spi flash
   -  short spi pin1 and pin2
   -  connect USB to PC
   -  found device: 
      -  usb 4-1.2.4.4: New USB device found, idVendor=1f3a, idProduct=efe8
   -  release spi pin1 and pin2
   -  flash command: 
      -  $ sudo sunxi-fel -p spiflash-write 0 u-boot-sunxi-with-spl.bin
-  boot from SDCard
   -  $ sudo dd if=u-boot-sunxi-with-spl.bin of=/dev/sdX bs=1024 seek=8
  
### flash kernel
-  copy boot.scr into partition 1
-  copy zImage into partition 1
-  copy suniv-f1c500s-miyoo.dtb into partition 1
-  copy r61520fb.ko into kernel folder in partition 2
-  copy daemon into kernel folder in partition 2
  
### build rootfs
-  download buildroot-2018.02.9 from https://buildroot.org
-  use config_buildroot-2018.02.9(in devel.zip) and then make it
-  toolchain location: /opt/miyoo
-  rootfs location: output/images/rootfs.tar
  
### flash rootfs
-  extract rootfs.tar into Partition 2
  
### https://steward-fu.github.io/website/index.htm
