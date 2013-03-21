---
comments: true
date: 2010-08-18 11:20:04
layout: post
slug: install-openvpn-on-moto-milestone
title: 在Milestone上安装openvpn
wordpress_id: 947
categories:
- Android
tags:
- android
- vpn
---

青蛙注：恨死ScribeFire，青蛙这篇日志写了一上午，结果马上要写完了，浏览器崩溃。杯具就是这么产生的。

重写的话，就不啰嗦了，直接写主要的。

青蛙目前使用的是机锋[fancies](http://bbs.gfan.com/space-uid-41092.html)大大制作的PLUS9 FINAL系统，可能其他的系统会有所区别，安装的时候需要注意。假设milestone已经获得了root，已经安装好了busybox，并且系统里已经带有tun.ko。

首先，要[下载一个openvpn](http://github.com/downloads/fries/android-external-openvpn/openvpn-static-2.1.1.bz2)，解压并上传到手机上，青蛙偏好使用adb push，这样不需要来回切换存储卡，插上usb就可以用。
`sudo adb push openvpn-static-2.1.1 /sdcard/openvpn`
使用`sudo adb shell`命令连到手机的shell，注意以下命令都需要root权限。
因为/system 是只读的，需要先挂载成可写的模式：
`# mount -o remount,rw -t yaffs2 /dev/block/mtdblock6 /system`
把openvpn复制到/system/bin里面：
`# cp -f /sdcard/openvpn /system/bin`
然后把/system重新挂载为只读模式：
`# mount -o remount,ro -t yaffs2 /dev/block/mtdblock6 /system`

打开Market，搜索openvpn，安装openvpn settings，然后打开openvpn settings，按菜单键选择Advanced：
选中Load tun kernel module，Path to configuations设置为/sdcard/openvpn，Path to openvpn binary设置为/system/bin/openvpn。
[![openvpn-settings-1](http://gfrog.net/pic/android/image/openvpn-settings-1.jpg)](http://gfrog.net/pic/android/openvpn-settings-1.jpg)
然后点击TUN module settings，
Load module using选择insmod，path to tun module填入/system/lib/modules/tun.ko
[![openvpn-settings-2](http://gfrog.net/pic/android/image/openvpn-settings-2.jpg)](http://gfrog.net/pic/android/openvpn-settings-2.jpg)
为了防止DNS欺骗，还需要Fix DNS，点击Fix DNS，会把dns服务器设置为8.8.8.8。
[![openvpn-settings-3](http://gfrog.net/pic/android/image/openvpn-settings-3.jpg)](http://gfrog.net/pic/android/openvpn-settings-3.jpg)

完成以上设置之后，把openvpn的配置文件、证书等复制到/sdcard/openvpn目录下，再打开Openvpn Settings，就可以连接vpn了。

**PS1.** 青蛙开始的时候是用Market里面的Openvpn Installer安装openvpn的，但是装完之后可以建立vpn连接，但是无法设置tun设备，log里会出现这样的错误日志：
`Options Error: Unrecognized option or mising parameter(s) in [PUSH-OPTIONS]:1: route (2.1.1)
Options Error: Unrecognized option or mising parameter(s) in [PUSH-OPTIONS]:1: ifconfig (2.1.1)`
解决方法也很简单，按照上面描述的方法，下载一个openvpn程序装上就行了[1]。

**PS2.** 网上有些教程[2]使用的是TunnelDroid，青蛙感觉这个东东跟Openvpn Settings基本功能都一样，只不过TunnelDroid可以更方便的查看Log，
这两个程序按照自己的喜好安装就是了。

参考文档：



	
  1. Issue 26: Unrecognized Command or missing parameter [http://code.google.com/p/android-openvpn-settings/issues/detail?id=26](http://code.google.com/p/android-openvpn-settings/issues/detail?id=26)

	
  2. android手机moto Milestone 配置OpenVPN方法 [http://www.yisiyisi.net/archives/137.html](http://www.yisiyisi.net/archives/137.html)


