---
title: hikey初始化和镜像下载
date: YYYY-MM-DD HH:mm:ss
categories: 
- hikey
tags:
- 1、下载镜像
---
详见：

- https://github.com/96boards/documentation/wiki/HiKey-Board-Recovery
- https://github.com/96boards/documentation/wiki/HiKeyUEFI

# 1、下载镜像

```bash
# hikey Android Open Source:相关镜像下载方式：
wget http://builds.96boards.org/releases/hikey/linaro/binaries/latest/l-loader.bin
wget http://builds.96boards.org/releases/hikey/linaro/binaries/latest/fip.bin
wget http://builds.96boards.org/releases/hikey/linaro/binaries/latest/nvme.img
wget http://builds.96boards.org/releases/hikey/linaro/binaries/latest/ptable-aosp-8g.img
wget https://builds.96boards.org/releases/hikey/linaro/aosp/latest/boot_fat.uefi.img.tar.xz

xz -d boot_fat.uefi.img.tar.xz
tar -xf boot_fat.uefi.img.tar
```

# 2、下载烧写工具

```bash
wget https://raw.githubusercontent.com/96boards/burn-boot/master/hisi-idt.py
```

***注意：*** hikey烧写工具需要Python 2.7环境
在烧写之前，需要用条线帽短接几个管脚：

就是SD卡插口以上的管脚，功能分别是：

- 1-2：短接：功能是上电自动启动单板
- 3-4：短接：功能是自举模式升级，就是烧写UEFI等镜像。
- 5-6：短接：功能是在串口终端提供选择启动方式(SD卡启动、EMMC启动、USB启动)菜单，如果不选择10秒后会自动默认方式启动。

关于西面四个管脚是UART0：

- `管脚1：` 1.8V电压
- `管脚2：` UART0_RX
- `管脚3：` DGND接地
- `管脚4：` UART0_TX

如果是裸板，则需要短接`1-2`和`3-4`，运行如下命令烧写镜像：

```bash
sudo python hisi-idt.py -d /dev/ttyUSB0 --img1=l-loader.bin
sudo fastboot flash ptable ptable-aosp-8g.img
sudo fastboot reboot
sudo python hisi-idt.py -d /dev/ttyUSB0 --img1=l-loader.bin (again, remember ttyUSB* can change)
sudo fastboot flash fastboot fip.bin
sudo fastboot flash nvme nvme.img
sudo fastboot flash boot boot_fat.uefi.img
```



# 启动顺序

- Android Fastboot App
- grub on eMMC
- SD

***注意：*** sdcard是最高优先级的启动方式，如果没有sdcard则从emmc启动，`5-6`短接才是Android Fastboot App最先启动
启动顺序是可以修改的，用如下命令修改：

```bash
sudo fastboot oem bootdevice [emmc|sd]
By default, SD card is the boot device. The boot flow is in below.
    a. If boot device is SD card.
         1) If SD card is present, boot from SD card.
         2) If SD card is _not_ present, boot from eMMC.
    b. If boot device is eMMC.
         Always boot from eMMC whether SD card is present or not. 
         It's used when SD card is just mass storage device. 
         For this case, people always leave SD card in slot.
```



# 生成fastboot序列号

```bash
sudo fastboot oem serialno
sudo fastboot oem serialno set {serialno string, 16 characters long}
```

# 控制led灯

```bash
sudo fastboot oem led1 on
sudo fastboot oem led1 off
```

# 自动启动设置

```bash
By default, auto boot feature is enabled.
Command to disable auto boot:
$sudo fastboot oem autoboot 0
Command to enable auto boot:
$sudo fastboot oem autoboot 1
```

# 怎么修改官方提供的boot_fat.uefi.img：

```bash
mkdir -p boot-fat
sudo mount -o loop,rw,sync boot_fat.uefi.img boot-fat
# command to copy custom kernel & dtb file into boot-fat
sync
sudo umount boot_fat.uefi.img
```

# kernel使用UART0作为log输出：

```bash
# Update kernel command line in grub.cfg
linux ($root)/boot/Image console=tty0 console=ttyAMA0,115200 console=ttyAMA3,115200 root=/dev/disk/by-partlabel/system rootwait rw quiet efi=noruntime
# Don't need to build kernel again.
```

# 创建或修改GRUB里的启动项

GRUB是操作系统的加载器，现在hikey的debian和aosp都是通过GRUB加载启动的，
如果你想运行自己的kernel，那么就要修改GRUB项。
grub.cfg存储在镜像boot_fat.uefi.img的/EFI/BOOT目录下，在grub.cfg里添加新项。
示例：仅仅修改kernel和dtb文件，任然使用initrd。

```bash
menuentry 'Custom Kernel' {
    search.fs_label rootfs root
    search.fs_label boot esp
    linux ($esp)/Image console=tty0 console=ttyAMA3,115200 root=/dev/disk/by-partlabel/system rootwait rw efi=noruntime
    initrd ($root)/boot/initrd.img
    devicetree ($esp)/hi6220-hikey.dtb
}
```