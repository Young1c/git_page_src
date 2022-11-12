---
title: UEFI for RPi4
date: 2022-11-12 15:25:55
tags:
---

# UEFI 固件获取及安装

EDK2已经支持Raspberrypi 4的UEFI,可以直接下载源码编译安装.

也可以下载私人[预安装版本](https://github.com/pftf/RPi4),安装方法已经在README中说明.

1. 格式化SD卡并进行分区, 将UEFI固件copy到ESP分区
2. 插入Pi4, 并启动
3. 可以看到进入UEFI界面

# Ubuntu安装

1. 准备一个U盘,烧入ubuntu镜像.
2. 启动树莓派,在UEFI的Boot Manager中选择通过USB启动
3. 进入Ubuntu安装界面. 需要首先在shell下备份ESP分区下的UEFI固件及其他文件, 因为安装过程
   中会将整个SD进行格式化并重新分区. 默认安装的固件无法直接启动(猜测和测试,原理未知)
4. 返回Ubuntu的Installer界面, 将Ubuntu安装到SD卡上
5. 完成安装后,返回shell界面,将我们备份的UEFI固件等文件复制到EFI分区下.(两个目录,
   /boot/efi以及/boot/efi/EFI/ubuntu下. 应该只需要/boot/efi就可以了,但是没有测试.
   保险起见两个都复制了)
6. 重新启动.可以进入grub界面,选择ubuntu entry即可进入安装的ubuntu了

需要注意的是,ubuntu默认不会输出日志到串口.重启前(或者在grub界面)需要编辑启动cmdline,将
日志输出到串口(console=ttyS0,115200 earlycon).

当前发现通过UEFI启动Linux要比原生的启动方式慢非常多,主要是1)从EFI进入grub的过程 2)从grub
进入内核的过程比较慢. 对于2)怀疑可能是initramfs加载比较慢(测试时使用的initramfs有约100M,
按道理应该不会很慢才对). 这里有一篇[文档](https://zhuanlan.zhihu.com/p/28708585)讲述如何
提高UEFI启动内核的速度,直接通过将Linux编译成UEFI应用进行启动.从而跳过grub的加载流程.

# Other references

本来想装Fedora的，搞了半天没搞定。这篇[指导](https://github.com/glacion/fedora-chroot-installation/blob/master/docs/VM-Install.md)
也写得不错.

后来分析了,主要是Pi4的固件没有搞定,一定要用Pi4的固件启动.发行版安装过程中安装的UEFI固件
可能有问题.

另外这些发行版(Ubuntu,Pi4)都没有脚本安装知道,只好用它默认的安装程序,然后再修复固件了.
这点上Arch就做的不错,又非常详细的脚本[安装指导](https://wiki.archlinux.org/title/Installation_guide),
可以全程手动操作,能够知道每一步需要做哪些操作,有什么作用.可惜Arch专注于x86,并没有官方
的arm64版本.
