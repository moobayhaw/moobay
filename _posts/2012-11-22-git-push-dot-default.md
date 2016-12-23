---
layout: post
title: "GIT设置push.default"
date: 2012-11-22 14:50
comments: true
categories: 
---

今天使用<code>git push</code>命令，结果出现一堆以前没见过的东西

>warning: push.default 未设置，它的默认值将会在 Git 2.0 由 'matching'
修改为 'simple'。若要不再显示本信息并在其默认值改变后维持当前使用习惯，
进行如下设置：
>
>  git config --global push.default matching
>
>若要不再显示本信息并从现在开始采用新的使用习惯，设置：
>
>  git config --global push.default simple
>
>参见 'git help config' 并查找 'push.default' 以获取更多信息。
（'simple' 模式由 Git 1.7.11 版本引入。如果您有时要使用老版本的 Git，
为保持兼容，请用 'current' 代替 'simple' 模式）

尼玛，乍一看吓一跳还以为代码出错了，解决这个问题很简单，按照上面的提示在Terminal.app里面运行一下脚本

```bash
git config --global push.default matching
```

我在纳闷，GIT版本更新后为毛不自动设置一下.gitconfig配置呢？还要我手动操作一下，难道是想介绍GIT的这新功能？