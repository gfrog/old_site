---
comments: true
date: 2008-03-20 00:30:34
layout: post
slug: markdown-syntax-basic
title: Markdown语法摘要
wordpress_id: 302
categories:
- VIM
tags:
- Markdown
- syntax
---

[Markdown](http://daringfireball.net/projects/Markdown/)是John Gruber设计的一个文本标记系统，相比html，它很简单，便于手工编写，而且它还支持[Wordpress](http://wordpress.org/)，[有一个用于wordpress的插件](http://michelf.com/projects/php-Markdown/)。青蛙决定用vim+Markdown来编写blog，看起来他们是一对强大的组合 :)

首先要去[Vim](http://www.vim.org/)的官方网站[下载Markdown的语法高亮插件](http://www.vim.org/scripts/script.php?script_id=1242)，并把它复制到"~/.vim/syntax/"目录下面。然后，新建一个"~/.vim/ftdetect/mkd.vim"文件，在其中加入下面的内容：


> " markdown filetype file
if exists("did_load_filetypes")
finish
endif
augroup markdown
au! BufRead,BufNewFile *.mkd   setfiletype mkd
augroup END


并在"~/.vimrc"文件中添加如下内容：


> "Markdown language syntax settings
augroup mkd
autocmd BufRead *.mkd  set ai formatoptions=tcroqn2 comments=n:>
augroup END


然后，后缀名为".mkd"的文件就能被自动启用Markdown的语法高亮了。

下面是Markdown语法的简要列表，便于以后查阅。以下内容主要从[牧码志](http://www.leninlee.cn)整理，感谢leninlee的辛勤劳动。



	
  * 链接的添加，共有三种形式，Markdown支持相对链接。


> <链接> ：这种形式只是简单的标出链接；
[文字](链接 "标题")：给文字添加链接，其中标题是可选的；
[文字][标记]：给文字添加链接，链接在下面的一个以[标记]开头的新行（一般是文章末尾）给出。或者直接以[文字][]这种形式编写，在下面的新行中使用[文字]:链接的形式声明链接。




	
  * 图片的添加：跟链接比起来，图片只是多了在前面多了一个叹号"!"。

	
  * 标题格式：Markdown提供了2种方式。


> 第一种是在标题前面加上1~6个"#"表示"<h1>"~"<h6>"。
第二种是在标题下面另起一行，输入四个以上的等号“=”来表示"<h1>"，输入四个以上的减号“-”来表示"<h2>"。




	
  * 粗体和斜体：用星号"*"或者下划线"_"


> 一个表示斜体；
两个表示粗体；
三个表示粗斜体。




	
  * 引用内容：就是html标记的"<blockquote>"，只要在行首用右尖括号>就行了。如果要嵌套引用，那就多打几个>。

	
  * 原文输出：如果不想Markdown解释某些内容，有两种方式：


> 第一个是转义为html的"<pre>"标签：要在要原样输出的内容前面加入至少4个空格或者1个tab的宽度；
第二个是转义为"<code>"标签：给要输出的内容加上"`"号（就是esc下头那个键）。




	
  * 无序列表：在文字前面使用星号"*"、加号"+"、减号"-"中的任意一个，注意在这些符号后面要留一个空格。

	
  * 有序列表：在第一条前添加一个数字，后跟一个英文句点"."。无论第一个条目前加的数字是什么，列表都会从1开始计数。

	
  * 转义符：""。只要给不希望被转义的字符前面加上就可以了。

	
  * HTML实体：如果要在内容中输出"<"或者"&"符号，那么必须用[HTML实体][]代替。Markdown会把HTML的特殊符号直接翻译为HTML实体。

	
  * 换行符：Markdown中在一行的末尾使用两个以上的空格标示HTML中的一个换行符"<br/>"。

	
  * 段落：只要两行之间有一个空行，Markdown就会把它识别为一个段落。
（青蛙在写这篇文章的时候发现，在列表之前一定要加入一个空行，要不然Markdown是不会把"*"识别为列表的。）

	
  * 水平线：使用三个以上的"*"或"-"来表示。这些星号跟减号之间可以用空格，如果减号没有空格，
那它必须在单独的一个段落里，要不它会被识别为标题的。

	
  * 直接使用HTML标记：有些Markdown不支持的标签可以直接写HTML标记，例如表格。


**update,2008-03-20:** 青蛙发现直接向wordpress写Markdown格式效果真的很差，而且不方便编辑，所以青蛙决定还是在本地把Markdown文档转化为HTML格式再发布到blog上好了。
