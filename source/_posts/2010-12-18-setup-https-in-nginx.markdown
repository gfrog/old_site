---
comments: true
date: 2010-12-18 15:45:25
layout: post
slug: setup-https-in-nginx
title: 在NGINX上开启https
wordpress_id: 1081
categories:
- linux[Linux]
- gfrog-say[青蛙的杂烩]
---

青蛙的新VPS是基于nginx的，仅仅是因为传言nginx在小内存，大压力的时候表现好一些。当然，为了获得这点好处，还是要付出一些代价的，例如nginx没有apache的htaccess，对每个目录的控制都需要写到nginx的配置文件中，这样对普通用户来说十分的不方便。

哦，有点跑题，继续说https的问题，其实给nginx配置https也不复杂，按照网上现成的模板，改一下自己的域名和目录位置就好了。

下面是一个完整的示例[1][2]：（来自[这里](http://sudone.com/nginx/nginx_ssl.html)）


> upstream test.com {
server 192.168.1.2:80;

}
server {
server_name test.com;
include port.conf;

ssl on;
ssl_certificate server.crt; # ${nginx_install_dir}/conf/server.crt
ssl_certificate_key server.key; # ${nginx_install_dir}/conf/server.key
error_page 497 "https://$host$uri?$args";

location / {
proxy_pass http://test.com;
include proxy.conf;#
auth_basic "status";
auth_basic_user_file trac.passwd;
}

}


**PS. **按照[delphij大神的说法[3]](https://blog.delphij.net/2010/07/nginxtlsssl.html)，nginx可以在一个IP上对使用不同证书的站点提供https支持。ubuntu上的nginx正好打开了这个支持。

**PS2.** 有了https支持，青蛙就可以使用https方式打开wordpress后台了，也很简单，在wp-config.php里面加一句[4]：


> define('FORCE_SSL_ADMIN', true);


参考资料：

[1] [nginx ssl加密代理配置指南](http://sudone.com/nginx/nginx_ssl.html)
[2] [Nginx 配置 SSL 证书 + HTTPS 站点小记](http://zou.lu/nginx-https-ssl-module/)
[3][nginx中的TLS/SSL配置](https://blog.delphij.net/2010/07/nginxtlsssl.html)
[4] [How to: Force using SSL on wp-admin directory](http://www.wprecipes.com/how-to-force-using-ssl-on-wp-admin-directory)
