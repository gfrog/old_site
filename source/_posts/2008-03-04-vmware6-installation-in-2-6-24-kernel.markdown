---
comments: true
date: 2008-03-04 00:43:44
layout: post
slug: vmware6-installation-in-2-6-24-kernel
title: 历经坎坷，安装VMWARE6
wordpress_id: 294
categories:
- linux[Linux]
---

青蛙被vmware郁闷了整整一晚上，遇到问题无数，google了一大把关键字，看了一大叠邮件列表，终于把它在2.6.24内核上给装上了。赶紧写下来，免得以后遇到类似的问题再抓瞎。

（请注意青蛙给出命令的提示符，$表示不要求root权限，#表示必须用root权限执行）


> 1. 到[http://www.vmware.com/download/ws/](http://www.vmware.com/download/ws/)这里vmware6的安装包下载回来，总共有200多M。




> 2. 下载安装包的同时，还需要准备一些包：`#aptitude install linux-source gcc-4.1 g++-4.1 libncurses5-dev `这些是linux源码包，还有gcc跟g++，待会编译模块会用到。




> 3. 解压vmware6的安装文件$tar zxvf VMware-workstation-6.0.2-59824.i386.tar.gz。




> 4. $cd  vmware-distrib，然后执行#./vmware-install.pl。一路回车，到提示是否执行/usr/bin/vmware-config.pl这步时选择no。




> 5. $cd /usr/src，然后#tar jxvf linux-source-2.6.24.tar.bz2，再#ln -s linux-source-2.6.24 linux。




> 6. #cd linux，#make menuconfig，在General setup ->  Local version - append to kernel release选项中填入正在运行内核的子版本号（例如，青蛙的内核版本为2.6.24-1-686,那么就在这里填入-1-686，在这里青蛙困扰了好久，这回终于知道怎么写这个版本号了 :mrgreen: ）,然后保存退出，执行#make prepare。




> 7. 在[这里](http://vmkernelnewbies.googlegroups.com/web/vmware-any-any-update-116.tgz)（[这里](http://blog.creonfx.com/temp/vmware-any-any-update-116.tgz)和[这里](http://linuxtoy.org/files/vmware-any-any-update-116.tgz)也分别有下载）下载vmware-any-any-update-116.tgz这个补丁。并解压：$tar zxvf vmware-any-any-update-116.tgz，然后$cd vmware-any-any-update116，执行#./runme.pl，当询问是否运行vmware-config.pl的时候，选择yes。（如果以上步骤执行正确，到这里应该能正确的编译vmware需要的各种模块了，如果出错，请google错误信息，一般都有结果的。）

>
>> **update:2008-03-18. **今天青蛙再次在2.6.24内核上面安装vmware，编译模块时又出现了下面的错误信息：

>>
>>>  /bin/sh: scripts/genksyms/genksyms: 没有那个文件或目录
make[2]: *** [/tmp/vmware-config2/vmmon-only/linux/driver.o] 错误 1
make[1]: *** [_module_/tmp/vmware-config2/vmmon-only] 错误 2
make[1]: Leaving directory `/usr/src/linux-source-2.6.24'
make: *** [vmmon.ko] 错误 2
make: Leaving directory `/tmp/vmware-config2/vmmon-only'
Unable to build the vmmon module.
>> 
>> 
 青蛙在源码目录执行了一下#make all，把genksyms这个模块编译一下就好了，真是一个很诡异的问题。
> 
> 
8. 接下来就是配置虚拟网卡，按照自己的爱好配置就可以了。

9. 这一切都完成以后，会有一长串的许可协议冒出来，到最后输入yes就行了。

10. 到这里，vmware就算完全安装完毕了，在gnome的菜单里找到vmware workstation运行之，然后输入序列号，就可以安装虚拟系统了。


至于序列号，google上是，一抓一大把，青蛙就不提供了。 祝大家玩的愉快。 :smile:

文中提到的vmware-any-any-update-116.tgz补丁的下载：

[vmware-any-any-update-116.tgz (vmware-any-any-update-116.tgz)](http://www.divshare.com/download/3938296-131)

参考文档：

[http://blog.creonfx.com/linux/how-to-install-vmware-player-workstation-on-2624-kernel](http://blog.creonfx.com/linux/how-to-install-vmware-player-workstation-on-2624-kernel)

[http://linuxtoy.org/archives/vmware-and-kernel-2624.html](http://linuxtoy.org/archives/vmware-and-kernel-2624.html)

[http://linux.bloghome.cn/posts/159172.html ](http://linux.bloghome.cn/posts/159172.html)
