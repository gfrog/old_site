---
comments: true
date: 2011-01-08 01:41:07
layout: post
slug: in-reply-to-id-bug-of-twitter-digest-plugin
title: 修复twitter digest插件的无法正确显示in-reply-to-id的bug
wordpress_id: 1617
categories:
- site-maint[建站相关]
tags:
- php
- twitter
- wordpress
---

青蛙一直在用twitter digest插件来把tweets同步到wordpress上。今天检查同步过来的tweets的时候，青蛙发现所有回复其他人的推中，in reply to的链接都失效了，tweet id变成了科学计数法的形式。
这个问题是由于twitter digest没有正确处理这么大的tweet id造成的，青蛙在网上搜了一下处理php大整数的方法[1]，发现只需要把这个整数sprintf成一个string就好了。
具体的修改方法：

    
    --- twitter-digest-old.php  2011-01-07 17:35:01.000000000 +0000
    +++ twitter-digest.php     2011-01-07 17:16:34.000000000 +0000
    @@ -281,7 +281,7 @@
        $output .= ws_make_clickable(wp_specialchars($tweet->text));
        if (!empty($tweet->in_reply_to_screen_name)
            && (!empty($tweet->in_reply_to_status_id))) {
    -    $output .=         ' <a href="'.ws_status_url($tweet->in_reply_to_screen_name, $tweet->in_reply_to_status_id).'">'.sprintf(__('in reply to %s', 'twitter-digest'), $tweet->in_reply_to_screen_name).'</a>';
    +    $output .=         ' <a href="'.ws_status_url($tweet->in_reply_to_screen_name, sprintf('%1.0f', $tweet->in_reply_to_status_id)).'">'.sprintf(__('in reply to %s', 'twitter-digest'), $tweet->in_reply_to_screen_name).'</a>';
        }
        // Show the date/time if the options are selected
    



参考资料：
[1] PHP 大数自动转换为科学计数法的无耻问题 [http://mifunny.info/big-number-convert-to-scientific-notation-of-php-341.html](http://mifunny.info/big-number-convert-to-scientific-notation-of-php-341.html)
