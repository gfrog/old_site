---
comments: true
date: 2008-01-26 16:35:02
layout: post
slug: kde-beyond-linux-windows-and-mac-os-x-desktop-environment
title: KDE：跨越Linux、Windows和Mac OS X的桌面环境
wordpress_id: 285
categories:
- linux[Linux]
---

![](http://pic.yupoo.com/gfrog/855075025a63/odu22jqq.jpg) KDE，这个Linux/Unix下华丽丽的桌面环境，正在走向Windows和Mac OS X。一些核心的KDE程序，还有其他重要的应用软件，例如KOffice和Amarok，已经开始向这两个平台移植。

 

这些成就除了因为KDE4的新特性，例如Phonon和Solid，还因为[Trolltech在Qt4的windows版本中也采用了GPL许可](http://trolltech.com/products/qt/licenses/licensing/opensource)。大家都知道，Qt使用双许可证，对于开源软件的开发可以使用GPL许可证，对于专有软件就需要用商业许可来授权。但是，在Qt以前的版本中，只有Mac OS X和Linux/X11版本才有GPL许可。这次伴随着Qt4的发布，windows版本的Qt也终于可以使用GPL许可证了。这个变化最终打开了开源的KDE应用程序通向windows的大门。

 

当然，KDE开发社区使用[CMake](http://www.cmake.org/HTML/Index.html)来替代Autotools也是使KDE走向这三大系统的一个重要原因。

 

##### **Windows下的KDE**

 

现在（其实是从1月22号起），[从KDE的镜像站点中就能下载到二进制的Windows版本的KDE环境](http://windows.kde.org/news.php#itemKDEmirrorsavailableandnewsnapshotrelease)了 。从[这里](http://download.cegit.de/kde-windows/installer/kdewin-installer-gui-latest.exe)可以下载到一个安装程序，用户可以通过这个小程序选择自己需要的KDE包。这个小程序可以自动解决包依赖问题，下载并安装需要的包。

 

现在windows版本的KDE看起来更像是mingw的一个超集，它里面包含了很多mingw的应用程序，例如gcc、gdb、tar、patch等等。再加上KDE自身的应用程序，就构成了现在的windows版KDE。

 

青蛙在自己的机器上安装了一下，简单介绍一下安装过程。

 

[![kdewin-1](http://pic.yupoo.com/gfrog/03228502519b/medium.jpg)](http://www.yupoo.com/photos/view?id=ff80808117c46cc20117c489d7d20583) [![kdewin-2](http://pic.yupoo.com/gfrog/6026550251a0/medium.jpg)](http://www.yupoo.com/photos/view?id=ff80808117c46cc20117c489ee170585) [![kdewin-3](http://pic.yupoo.com/gfrog/8888050251a2/medium.jpg)](http://www.yupoo.com/photos/view?id=ff80808117c46c760117c489f3cc055c) [![kdewin-4](http://pic.yupoo.com/gfrog/4605450251a4/medium.jpg)](http://www.yupoo.com/photos/view?id=ff80808117c46c190117c48a16be038d) [![kdewin-5](http://pic.yupoo.com/gfrog/8885050251a7/medium.jpg)](http://www.yupoo.com/photos/view?id=ff80808117c46c2a0117c48a07d60542) [![kdewin-6](http://pic.yupoo.com/gfrog/3916650251ae/medium.jpg)](http://www.yupoo.com/photos/view?id=ff80808117c46cc20117c48a2044058a) [![kdewin-7](http://pic.yupoo.com/gfrog/7352550251b3/medium.jpg)](http://www.yupoo.com/photos/view?id=ff80808117c46cc20117c48a36c9058c) [![kdewin-8](http://pic.yupoo.com/gfrog/237275025197/medium.jpg)](http://www.yupoo.com/photos/view?id=ff80808117c46bd70117c489e353027e) [![kdewin-9](http://pic.yupoo.com/gfrog/752965025b96/medium.jpg)](http://www.yupoo.com/photos/view?id=ff80808117c46b110117c4b0f7691015)

 

结果装上以后所有的程序都没法执行，提示缺少libkdecore.dll文件。把所有的包装上也没有这个文件，看来这应该是个bug了，难道是跟青蛙只选择了MinGW的文件有关？这样进一步的测试也就没法进行了。只好等待它的新版本了。
