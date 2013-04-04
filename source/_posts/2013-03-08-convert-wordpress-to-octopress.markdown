---
layout: post
title: "Wordpress转换到Octopress"
date: 2013-03-08 21:11
comments: true
categories: 
 - gfrog-say[青蛙的杂烩]
 - site-maint[建站相关]
---

今天青蛙下定决心干掉Wordpress了，不为别的，就因为
在大天朝访问Openshift不靠谱的连接状态。

把Wordpress转换到Octopress这件事，对于天天泡在github
上的青蛙来说，实在是简单到爆了。github账户有了，ruby
运行环境有了，只要简单几步就够了：

 1. 按照 [http://octopress.org/docs/setup/](http://octopress.org/docs/setup/)
    git clone回octopress代码，安装bundler，安装默认主题。
 1. 按照 [http://octopress.org/docs/deploying/github/](http://octopress.org/docs/deploying/github/) 
    在github上新建一个repo，名字取为"your_github_id.github.com"，在Octopress的目录中执行
    `rake setup_github_pages`， 在提示符中填入这个新建的repo的git地址。
 1. 然后
        rake generate
        rake deploy

 1. 用浏览器访问"http://your_github_id.github.com"，应该就能看到Octopress的页面了。
 1. 记得要把源码（包括blog的markdown文件）提交到git repo里： 包括blog的markdown文件
        git add .
        git commit -m 'your message'
        git push origin source

    之后每次写完新post都需要做这个操作，以便保留原始文档方便日后编辑。

 1. 编辑\_config.yml文件，修改blog的title、author、email等等信息。
    [http://octopress.org/docs/configuring/](http://octopress.org/docs/configuring/)
 1. Octopress 配置好之后就是把Wordpress迁移过来了，青蛙找到了一个exitwp的工具：
    [https://github.com/thomasf/exitwp](https://github.com/thomasf/exitwp)
    按照exitwp的README安装好之后，从wordpress导出xml格式的文章备份，首先用xmllint检查
    一下这个xml文档是不是有语法错误，特别是特殊字符之类。因为exitwp用了python的xml module，
    这个模块对某些错误极度敏感，所以要修正xmllint报出的所有错误才行。
 1. 把这个xml扔进exitwp里面的wordpress-xml目录，执行exitwp，所有的wp post就会被转换成
    一个个markdown文件了。
    把这些markdown复制到octopress目录里的source/\_posts下面，然后运行
    <code>rake generate && rake deploy</code> 就可以把Wordpress的post内容都迁移到Octopress上了。
 1. 最后记得把这些markdown都扔进source branch里。

目前青蛙还没解决的几个问题：

 1. Category的英文名字似乎没法编辑，青蛙还没找到相关的文档。
 1. Draft和private状态的文档还不清楚怎么转换。
 1. 当post很多时（>1000），`rake generate`需要运行相当长的时间，青蛙每次执行这个命令都需要2分钟+。


__Update 2013-03-16:__

Category的问题据说在Octopress2.1里面被解决了。

青蛙从2.1的branch上backport了一个patch到master，终于解决了中文category名字的问题。
patch在：
[https://github.com/gfrog/gfrog.github.com/commit/60212ee17766e8ba84f81cfdc3f36ef992ae6fb1](https://github.com/gfrog/gfrog.github.com/commit/60212ee17766e8ba84f81cfdc3f36ef992ae6fb1)
没有经过完整测试，不过目前使用状况良好。

目前Category的写法为 `- Category link[Category Name]`，从而完美搞定中文分类名的问题。


__Update 2013-04-03:__

补充一些Octopress用法的问题：

 1. 新建post： ` rake new_post["Post标题"] `，
    不过permalink似乎比较难修改，在markdown里写slug似乎无效，只能修改markdown文件名。
 1. codeblock 的用法： [http://octopress.org/docs/plugins/codeblock/](http://octopress.org/docs/plugins/codeblock/)
 1. 修改header menu： 编辑 source/\_includes/custom/navigation.html
 1. 添加Header image： 编辑 sass/custom/\_styles.scss，加入：
    {% codeblock lang:css %}
    header[role="banner"] {
         background-image: url(/images/header_bg.png);
         background-repeat: no-repeat;
    }
    {% endcodeblock %}
