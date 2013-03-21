---
comments: true
date: 2008-03-16 23:18:34
layout: post
slug: glinux-setup-build-directory
title: glinux开发摘要(2)-构建目录结构
wordpress_id: 297
categories:
- linux[Linux]
---

下面的操作步骤青蛙基本上是从[lfs6.3](http://www.linuxfromscratch.org/lfs/view/6.3)学来的，所以会有$LFS这个变量，如果你对lfs也有兴趣，可以[去它的官方网站](http://www.linuxfromscratch.org/)看看。





青蛙编译完成了toolchain以后，就可以开始建造glinux的目录体系了。
首先，构造虚拟内核文件系统(Virtual Kernel File Systems)，这个东东是跟内核通信的一个途径。





执行下面的命令，来建立文件系统的挂载点：





> 
  
> 
> mkdir -pv $LFS/{dev,proc,sys}
> 
> 






然后，建立几个必要的设备文件，





> 
  
> 
> mknod -m 600 $LFS/dev/console c 5 1   

  mknod -m 666 $LFS/dev/null c 1 3          
> 
> 






青蛙准备用udev来管理设备文件，所以只建立这两个文件就够了，其他文件udev会在系统启动的时候自动创建。
现在udev模块在glinux上还没有安装，所以暂时用mount --bind命令将宿主系统的/dev目录镜像到glinux的目录结构里来：





> 
  
> 
> mount -v --bind /dev $LFS/dev          
> 
> 






把几个重要的虚拟文件系统都挂在到glinux的目录上去：





> 
  
> 
> mount -vt devpts devpts $LFS/dev/pts   

  mount -vt tmpfs shm $LFS/dev/shm   

  mount -vt proc proc $LFS/proc   

  mount -vt sysfs sysfs $LFS/sys                        
> 
> 






以上几个步骤在每次重新chroot以后都要执行一遍。





上面的步骤都完成，历史性的时刻就要来了：chroot进入glinux。





> 
  
> 
> chroot "$LFS" /tools/bin/env -i HOME=/root TERM="$TERM" PS1='u:w$ '    

    PATH=/bin:/usr/bin:/sbin:/usr/sbin:/tools/bin /tools/bin/bash --login +h
> 
> 






看到命令提示符在喊“I have no name!”了么？如果看到它喊了，就说明chroot成功了 :)
进入新系统的第一件事就是要建立glinux的目录结构了，关于linux的目录结构，[linuxfans](http://www.linuxfans.org/)上面有[一篇文章介绍的非常详细](http://www.linuxfans.org/bbs/thread-182534-1-1.html)，青蛙执行以下命令来建立一个完整的目录结构：





> 
  
> 
> mkdir -pv /{bin,boot,etc/opt,home,lib,mnt,opt}   

  mkdir -pv /{media/{floppy,cdrom},sbin,srv,var}   

  install -dv -m 0750 /root   

  install -dv -m 1777 /tmp /var/tmp   

  mkdir -pv /usr/{,local/}{bin,include,lib,sbin,src}   

  mkdir -pv /usr/{,local/}share/{doc,info,locale,man}   

  mkdir -v  /usr/{,local/}share/{misc,terminfo,zoneinfo}   

  mkdir -pv /usr/{,local/}share/man/man{1..8}   

  for dir in /usr /usr/local; do   

   ln -sv share/{man,doc,info} $dir   

  done   

  mkdir -v /var/{lock,log,mail,run,spool}   

  mkdir -pv /var/{opt,cache,lib/{misc,locate},local}                             
> 
> 






然后，把/tools/目录下的一些文件链接到chroot环境对应的目录下：





> 
  
> 
> ln -sv /tools/bin/{bash,cat,echo,grep,pwd,stty} /bin   

  ln -sv /tools/bin/perl /usr/bin   

  ln -sv /tools/lib/libgcc_s.so{,.1} /usr/lib   

  ln -sv /tools/lib/libstdc++.so{,.6} /usr/lib   

  ln -sv bash /bin/sh                               
> 
> 






建立一个/etc/mtab文件：





> 
  
> 
> touch /etc/mtab                            
> 
> 






建立/etc/passwd文件和/etc/group文件：





> 
  
> 
> cat > /etc/passwd << "EOF"   

  root:x:0:0:root:/root:/bin/bash   

  nobody:x:99:99:Unprivileged User:/dev/null:/bin/false   

  EOF                             
> 
> 

  
> 
> cat > /etc/group << "EOF"   

  root:x:0:   

  bin:x:1:   

  sys:x:2:   

  kmem:x:3:   

  tty:x:4:   

  tape:x:5:   

  daemon:x:6:   

  floppy:x:7:   

  disk:x:8:   

  lp:x:9:   

  dialout:x:10:   

  audio:x:11:   

  video:x:12:   

  utmp:x:13:   

  usb:x:14:   

  cdrom:x:15:   

  mail:x:34:   

  nogroup:x:99:   

  EOF                                 
> 
> 






然后重新运行一次bash，让bash不再喊“I have no name!”





> 
  
> 
> exec /tools/bin/bash --login +h                                 
> 
> 






接下来再建立一些要用到的log文件：





> 
  
> 
> touch /var/run/utmp /var/log/{btmp,lastlog,wtmp}   

  chgrp -v utmp /var/run/utmp /var/log/lastlog   

  chmod -v 664 /var/run/utmp /var/log/lastlog                              
> 
> 






到这里，glinux的目录结构基本上搞定了，下面我们就可以制作内核跟busybox了。



