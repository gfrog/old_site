---
comments: true
date: 2010-12-21 02:23:08
layout: post
slug: setup-ssl-certificate-for-https
title: 为https服务申请SSL证书
wordpress_id: 1101
categories:
- gfrog-say[青蛙的杂烩]
tags:
- https
- ssl
- startssl
---

书接上文，上回书说到青蛙成功给nginx配置上了https服务，但是当时青蛙使用的是自签名证书，没有验证网站身份的功能；而且有些工具会对证书的有效性进行校验，对于这种工具，自签名证书就毫无用处了。这个时候，就需要申请一个经过CA认证的有效证书。

据青蛙所知，目前提供免费SSL证书的只有StartSSL[1]一家，青蛙按照这两个网站的介绍[2][3]，成功申请了SSL证书。

依照青蛙的经验，要成功申请证书，最好是有一个以要申请域名结尾的邮箱，这样可以很容易的确认域名所有权。至于邮箱服务，可以申请google app，方便，免费。

有了邮箱系统之后，可以设置一个webmaster或者hostmaster账户，以便注册过程中验证域名地址。

在StartSSL申请证书的时候有几点需要注意：
首先，建议使用firefox申请证书，青蛙用Chromium试了一下，点击进入Sign-up界面的时候会出现一个错误提示，明确说明Chrome类浏览器无法申请。有网友说IE也没法成功申请[1]。
其次，申请的时候尽量填写靠谱的个人信息，如果信息看起来不靠谱，很容易被拒掉。个人信息填写完之后会向你填写的邮箱里发送一封认证邮件，邮件中包含继续注册所需的验证码。输入验证码之后提示等待人工审核，青蛙遇到的审核过程很快，大概几分钟就有结果了。
接下来还有一封邮件，里面有一个链接和一个认证码，访问那个连接，填上认证码，如果一切正确StartSSL会自动在Firefox里面添加一个个人证书，这个证书就是用来登录StartSSL的控制台的。青蛙建议最好把它导出，找个安全的地方保存（毕竟StartSSL的免费证书1年就过期了，明年还得做一遍同样的流程再申请）。

以上是StartSSL的注册过程，下面是验证域名的过程。
进入StartSSL的控制台之后点击Validations Wizard，选择Domain Name Validation，然后填入要验证的域名，接下来需要选择一个邮箱，可以是postmaster/hostmaster/webmaster三个中的一个，或者是域名whois信息中的邮箱。选择一个，StartSSL会向这个邮箱发送一封确认邮件，填上邮件里的验证码，验证完成。注意这个验证有30天有效期，超过期限就需要重新认证域名。

接下来才是最重要的部分，点击Certificates Wizard开始申请证书。



	
  1. 这里有几钟证书可以选择：S/MIME、Web Server SSL/TLS、XMPP（jabber） SSL/TLS等，对于网站就是Web Server SSL/TLS证书了。

	
  2. 下一步是生成私钥的过程，青蛙没有网站提供的功能，而是选择了在本地生成私钥和证书请求文件csr的方式，所以这步选择跳过，然后在本地执行：

    
    # 生成私钥：
    $ openssl genrsa -aes256 -out private.key 2048
    # 这里需要输入一个密码，制作SSL证书密钥的时候会用到。
    # 生成证书请求文件：
    $ openssl req -new -key private.key -out cert.csr
    # 这里会询问一大堆信息，基本是证书的基本信息，例如域名、网站名称、所有者、地址等等。




	
  3. 然后打开cert.csr文件，复制所有内容贴到文本框中；

	
  4. 点击下一步继续，如果一切正常会提示证书请求已经正确提交了；

	
  5. 继续点击下一步，选择要为哪个域名申请证书；

	
  6. 再下一步，需要给这个证书添加一个子域名，青蛙选择了www，因为还要在www上提供https服务；

	
  7. 再继续，会有一个汇总，报告下要为哪些域名制作证书；

	
  8. 再点击继续开始正式制作证书。

	
  9. 等待证书制作完成，点击Tool Box，选择Retrieve Certficate，这里就是我们想要的网站证书啦；

	
  10. 把它复制到一个文件里，放在服务器上就可以用了。


然后是密钥，选择Decrypt Private Key，把之前生成的private.key文件打开，复制所有内容到文本框里，填入密码，点击Decrypt，接下来显示的文本框的内容就是网站证书的key啦，也复制出来保存到一个文件里，传到服务器上。

证书申请大功告成，接下来改一下nginx的配置，加入新申请的证书路径，用浏览器访问https站点试试，正确的话应该没有那个讨厌的https证书无效提示了。

**Update: 2012-01-12:**
StartSSL的登录证书的有效期竟然也是1年，所以如果需要更新SSL证书，还要重新Sign-up一次。

参考文档：
[1] www.startssl.com [http://www.startssl.com/](http://www.startssl.com/)
[2] Nginx + https + 免费SSL证书配置指南 [http://www.21andy.com/blog/20100224/1714.html](http://www.21andy.com/blog/20100224/1714.html)
[3] 免费SSL证书 STARTSSL 介绍与申请攻略 [http://dev.meettea.com/show-52-1.html](http://dev.meettea.com/show-52-1.html)
