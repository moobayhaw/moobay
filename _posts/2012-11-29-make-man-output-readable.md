---
layout: post
title: "MAN输出文本可读性增强技巧"
date: 2012-11-29 13:38
comments: true
categories: 
---

有时候在Terminal查看帮助实在是痛苦：只能显示几行，所以我就想在文本文件里面查看这些帮助（比如，TextMate），如果你使用类似<code>man col > ~/downloads/col.txt</code>，你会发现这简直是个悲剧，见下图 

![](/assets/images/make-man-output-readable/fig01.png)

这完全不是我想要的结果，没有任何可读性，这是怎么回事儿呢？我Google到了一段文字可以解释这个问题：

>Ever try to open a man page in TextEdit using <code>man | open -f</code>? 
>You end up with the kind of unreadable repeated characters shown here. This all dates back to the days of dot matrix and daisy wheel printing when the only way you could produce bold type was to repeatedly print characters. 
>Fortunately, there's an easy way to convert man pages into simple, non-redundant text. Use the command-line utility <code>col</code> with the <code>-b</code> flag enabled. For example, <code>man col | col -b | open -f</code> will open the col man page in TextEdit without repeated characters. The <code>-b</code> flag tells col to exclude all but the last character written to any column, ignoring any backspaces and repeats.

这是一个历史遗留问题，可以追溯到使用点阵和菊花轮印刷的旧时代，那个时候为了得到粗体字体样式采用的是反复重复印刷的手段。幸运的是，有个简单的方法可以让man帮助变得简单可读、没有冗余字符，可以在man后面使用管道添加<code>col -b</code>来实现，其中<code>-b</code>标记位意思是忽略退格键以及去掉所有重复字符。比如，得到<code>col</code>的帮助可以使用<code>man col | col -b | open -f</code>，这里<code>open -f</code> 意思是使用默认文本编辑器打开，如下图

![](/assets/images/make-man-output-readable/fig02.png)

最后总结一下：   

* man帮助输出到文本文件
> man [command] | col -b > [file saving path]

* man帮助直接用文本编辑器打开
> man [command] | col -b | open -f

