---
comments: true
date: 2010-12-22 23:53:26
layout: post
slug: enable-smtp-send-mail-in-wordpress
title: 配置wordpress的邮件发送功能
wordpress_id: 1113
categories:
- linux[Linux]
tags:
- mail
- plugin
- smtp
- wordpress
---

青蛙继续倒腾vps，今天的主题是调教wordpress发送邮件。

之前在Dreamhost上面，邮件系统已经配置好了，wordpress不需要任何设置就可以直接发送邮件。到了vps上之后，青蛙为了节省资源，没有安装邮件服务器，所以想要发送邮件，还需要自己折腾。
青蛙首先是想到了配置一个轻量级的MTA，例如msmtp之类的，然后修改php.ini的sendmail_path参数，使它指向msmtp。这样php程序就具有邮件发送能力了，但是这样产生的问题就是邮件的发送服务器是固定的，而且需要配置一个帐号来做登录服务器，如果某个用户希望使用自己的服务器发送某些邮件，这样配置就无法实现了。

为了实现完美的解决方案，青蛙又搜了一圈，发现了wordpress已经集成了一个PHPMailer[1]类，并且提供了一个wp_mail函数[2]来发送邮件，有很多设置SMTP的插件[3]方便我们配置SMTP服务器和外发邮件帐号信息。

青蛙选择了WP Mail SMTP插件，非常简单，把所有参数填好，wordpress就可以发送邮件了。

参考资料：
[1] PHPMailer [http://phpmailer.worxware.com/](http://phpmailer.worxware.com/)
[2] Function Reference/wp_mail [http://codex.wordpress.org/Function_Reference/wp_mail](http://codex.wordpress.org/Function_Reference/wp_mail)
[3] 让 WordPress 通过 SMTP 发送邮件 [http://goto8848.net/2008/04/wordpress-mail-via-smtp/](http://goto8848.net/2008/04/wordpress-mail-via-smtp/)
