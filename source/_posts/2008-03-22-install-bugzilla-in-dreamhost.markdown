---
comments: true
date: 2008-03-22 00:23:55
layout: post
slug: install-bugzilla-in-dreamhost
title: 在Dreamhost主机上安装bugzilla
wordpress_id: 301
categories:
- linux[Linux]
- site-maint[建站相关]
tags:
- bugzilla
- Dreamhost
- Linux
---

[bugzilla](http://www.bugzilla.org/)是[mozilla](http://www.mozilla.org/)一个很好的开源bug管理软件，青蛙准备在glinux的开发过程中用它来管理发现的bug。
但是dreamhost上没有bugzilla的自动安装功能，青蛙按照[dreamhost的wiki上面介绍的方法](http://wiki.dreamhost.com/Bugzilla#Installing_Bugzilla)安装时，
又有很多perl模块没有，而且在dreamhost的主机上也没有权限安装这些perl模块。所以，青蛙决定先在主机上自己搞一个perl。

perl的安装方法很简单，[dreamhost的wiki也有介绍](http://wiki.dreamhost.com/Perl#Path_to_the_Perl_interpreter)。
青蛙选择的是perl5.8.8，因为wiki上头说有一个针对dreamhost环境的补丁要打
（补丁文件在[这里](http://schwern.org/~schwern/src/dreamhost-5.8.8-cwd.patch)下载）。
打上补丁以后，执行：


> sh Configure -Dusethreads -Duselargefiles -Dccflags=-DDEBIAN 
-Dcccdlflags=-fPIC -Darchname=i386-linux -Dprefix=~/apps 
-Dpager=/usr/bin/sensible-pager -Uafs -Ud_csh -Uusesfio 
-Uusenm -Duseshrplib -Dvendorlib=/usr/share/perl5 
-Dvendorarch=/usr/lib/perl5 -Dvendorprefix=/usr -Uinstallusrbinperl -des


其中Dprefix参数要设置为要安装perl的目录。
config完毕就可以make && make install了。

下面正式开始安装bugzilla，（青蛙的perl在~/apps/bin目录下，这个目录要换成你自己实际的目录哦），首先进入bugzilla的目录，执行：


> ~/apps/bin/perl chechsetup.pl


这个脚本会检查当前的perl环境，如果有缺少的模块，它会给出安装命令。
例如，在青蛙的主机上，Email::Send、Email::MIME::Modifier就被提示安装。
按照这个脚本给出的命令把必须的模块安装上，然后再次执行上面的命令，会在bugzilla目录下生成一个localconfig文件。

修改这个localconfig文件：


> $webservergroup = "apache"中的apache改为自己真实的组名称，这个组名字可以用groups命令查到。
$db_host = "localhost" 修改为真实的mysql服务器地址。
$db_name = "bugs" 修改为真实的数据库名。
$db_user = "bugs" 修改为真实的用户名。
$db_pass = '' 修改为真实的密码。


上面几项都改完后，重新执行checksetup.pl脚本。这时脚本会询问管理员邮箱地址，设置管理员密码等等动作。

因为dreamhost使用suexec方式运行的cgi脚本，所以每次执行完checksetup.pl以后，都要执行以下命令：


> for i in docs graphs images js skins; do find $i -type d -exec chmod o+rx {} ; ; done
for i in jpg gif css js png html rdf xul; do find . -name *.$i -exec chmod o+r {} ; ; done
find . -name .htaccess -exec chmod o+r {} ;
chmod o+x . data data/webdot


完成这些后，bugzilla就算是安装成功啦。可以先用浏览器访问下bugzilla的地址看看效果。

接下来说一下汉化的问题，汉化包可以在[这里](http://bugzilla-cn.googlecode.com/)下载到，下载、解压以后，把cn文件夹复制到bugzilla目录里的template/目录就行啦。
