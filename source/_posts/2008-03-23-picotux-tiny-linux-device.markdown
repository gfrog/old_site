---
comments: true
date: 2008-03-23 21:50:56
layout: post
slug: picotux-tiny-linux-device
title: 世界上最小的linux
wordpress_id: 303
categories:
- linux[Linux]
- picture[美丽图片]
---

![picotux](http://www.picotux.com/pt100a.jpg)![](http://www.picotux.com/pt112a.jpg)

上面的图片就是号称世界上最小的linux：[picotux 100](http://www.picotux.com/techdatae.html)（右侧为picotux 112）。
这个小东西只有35mm×19mm×19mm大小，只比一个RJ45接口大了那么一点点。
它使用32-bit ARM 7 Netsilicon NS7520处理器，主频55MHz，
搭载2M~4M Flash和8M SDRAM，带有一个10/100M网卡，一个可以达到230.400 bps的串口，还有5个I/O针脚。
使用3.3V电压供电。

软件方面，它使用uClinux 2.4.27系统，系统大小最少720KB。Shell可以使用Busybox，使用CRAMFS、JFFS2文件系统, 还可以使用NFS，
有Webserver、Telnet等等程序。开发工具使用GUN Toolchain，使用GCC3.4.4以上版本和ulibc0.9.26以上版本。

这东东售价最低99欧元。
