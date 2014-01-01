---
comments: true
date: 2011-10-09 16:47:35
layout: post
slug: procmail-kdialog-mail-notifyer
title: procmail + kdialog 构建邮件提醒脚本
wordpress_id: 1872
categories:
- linux[Linux]
---

Mutt真的是神器，越用越觉得爽。可惜命令行模式的工具都有个弊端：跟X交互能力比较弱，例如Mutt就木有邮件提醒功能。之前在Gnome2的年代里还有[mail-notification](http://www.nongnu.org/mailnotify/)可以对付用（这玩意占用系统资源狂多，似乎是通过监视文件更改来提醒的）,在Gnome3之后这个工具就完全不工作了，而且又没有后续版本。青蛙抛弃Gnome，换成KDE之后，也没有找到KDE下的邮件提醒工具，于是一直酝酿着自己写一个。





实现邮件提醒功能，无非就是监控邮箱变更，可以在几个点检查：








	
  * 直接使用POP/IMAP协议检查服务器端

	
  * 监视MTA动作

	
  * 监视MDA动作

	
  * 监视MUA动作

	
  * 监视邮箱文件/目录更改




直接访问服务器是最直接的办法，但是既然MTA都把邮件收下来了，再去监视服务器就有点绕腾了，这种方法监视gmail/hotmail之类的邮箱还是很不错的。







监视MUA的动作，[adam8157](http://www.adam8157.info)同学[就是这么做的](http://www.adam8157.info/blog/2010/05/mutt-filter-notify/)，监视Mutt startusbar的特殊字符串，通过Mutt的filter发送notify消息。




监视邮箱文件/目录变化就是[mail-notification](http://www.nongnu.org/mailnotify/)的做法了，但是如果不想轮询的话，大概需要内核开启inotify支持。




监视MTA动作，青蛙没有仔细研究，似乎还是需要MDA的某些行为处理，所以青蛙直接用了在MDA里加入提醒脚本的方法。




监视MDA动作，就是青蛙的做法，利用procmail的nested block，把邮件pipe到一个脚本中处理，提取邮件发送者、标题和内容，然后发notify。







消息提醒的脚本已经有人做好了：




[http://blargasm.com/post/7980037487/evolution-new-mail-notifier](http://blargasm.com/post/7980037487/evolution-new-mail-notifier)




青蛙拿过来修改了一下，去掉pynotify，直接调用kdialog命令发送消息。




修改后的脚本在这里：




[http://code.google.com/p/gfrog/source/browse/mail/mail-notifier.py](http://code.google.com/p/gfrog/source/browse/mail/mail-notifier.py)







脚本有了，然后修改~/.procmailrc，在需要监视的过滤规则里面加入







> :0
# Some filter rule
{
:0 c
| ~/.mail-notifier.py
:0
#Here is your inbox
Inbox
}







这样就可以监视指定邮箱的新邮件啦：




![](http://gfrog.net/wp-content/uploads/2011/10/mail-notify.png)





**Update: 2012-06-11**
由于这个提醒脚本最终是通过cron运行的，并且经过几次fork之后，它没法找到当前的dbus session了，于是导致kdialog 使用旧式的KPassivePopup现实消息，但是这玩意是hardcode了消息显示在屏幕中心，相当不爽。青蛙google了很久，终于还是捏着鼻子看了下kdialog的代码才发现问题。
更新：
[https://code.google.com/p/gfrog/source/detail?r=fe8e9b57574529453fdb8655e0392260d39dcbfb](https://code.google.com/p/gfrog/source/detail?r=fe8e9b57574529453fdb8655e0392260d39dcbfb)

**Update: 2012-06-11**
继续更新，原始的mail notify脚本对于一些中文邮件subject和mail body的处理有问题，导致无法提醒中文邮件。目前青蛙的做法是把mail body当作utf-8字符串处理。当然这不能从根本上解决问题，只算是个workaround吧，还好青蛙的中文邮件不多。如果哪位读者遇到中文邮件还有问题，欢迎反馈bug。
同样，更新的代码在google code上：
[https://code.google.com/p/gfrog/source/detail?r=3de26a63648944313893d74b94f54f28770bed30](https://code.google.com/p/gfrog/source/detail?r=3de26a63648944313893d74b94f54f28770bed30)

**Update: 2011-10-14**
青蛙今天忽然发现这个脚本处理某些邮件的时候发件人那部分是空的，调查一下发现时因为“email.Utils.parseaddr()”实际上是把发件人处理成名称和邮箱名的一个list，如果发件人没有名字，只有一个邮箱名的话，最后显示的发件人部分就是空白了。青蛙修改了一下脚本，仍然放在了google code上：

[https://code.google.com/p/gfrog/source/detail?r=0c64c067cf047ad4390bc2e57875cacf328999f1](https://code.google.com/p/gfrog/source/detail?r=0c64c067cf047ad4390bc2e57875cacf328999f1)
