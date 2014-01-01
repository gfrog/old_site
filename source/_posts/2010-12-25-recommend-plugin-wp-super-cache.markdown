---
comments: true
date: 2010-12-25 00:22:10
layout: post
slug: recommend-plugin-wp-super-cache
title: 推荐插件-wp-super-cache
wordpress_id: 1490
categories:
- gfrog-say[青蛙的杂烩]
---

在wowubuntu！[1]上看到的wp-super-cache这个插件，青蛙立即拿来试验了一下，功效很显著。

没有优化前，青蛙用pingdom[2]测试主页面载入速度，需要将近4秒的时间。
优化过后是这样：
[![](http://gfrog.net/wp-content/uploads/2010/12/pingdom.jpeg)](http://gfrog.net/wp-content/uploads/2010/12/pingdom.jpeg)

接下来需要考虑的就是怎样减小那几个js文件的载入时间了，还没找到头绪，希望能得到大牛的指点 ：）

另外需要注意一下的是，如果是nginx环境安装wp-super-cache插件，需要的rewrite规则在[1]中有提供，但是那个版本是有问题的，这里[3]的版本才是正确的。
参考资料：
[1] 小内存 VPS 优化建议 (LNMP+WordPress 环境) [http://wowubuntu.com/vps-optimize.html](http://wowubuntu.com/vps-optimize.html)
[2] Pingdom tools [http://tools.pingdom.com](http://tools.pingdom.com)
[3] Code Exchange: nginx rewrite rules for Wordpress + WP Super Cache [http://forum.slicehost.com/comments.php?DiscussionID=2087](http://forum.slicehost.com/comments.php?DiscussionID=2087)
