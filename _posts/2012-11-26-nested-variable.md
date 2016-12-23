---
layout: post
title: "脚本中的嵌套变量"
date: 2012-11-26 20:38
comments: true
categories: 
---

## OSX系统

这里有一段简单脚本，

```sh
#!/bin/bash

name1=larry01
name2=larry02
name3=larry03
name4=larry04
name5=larry05
name6=larry06
name7=larry07
name8=larry08
name9=larry09

echo $name1 wants to sleep.
echo $name2 wants to sleep.
echo $name3 wants to sleep.
echo $name4 wants to sleep.
echo $name5 wants to sleep.
echo $name6 wants to sleep.
echo $name7 wants to sleep.
echo $name8 wants to sleep.
echo $name9 wants to sleep.

```

在脚本中，我把每一个name都按照一定的规律做了一些操作，现在看来好像没什么问题。但是如果name有很多很多，这么写就很苦逼了，并且很容易出错。<!--more-->我们习惯让电脑做重复有规律的工作，让人从重复的劳动中解脱出来。那么这是一个很好的案例，该怎么解脱呢？看起来很简单，有代码经验的同学很容易想到使用循环来实现，这个思路是对的，name1~name9这几个有规律的字符串是很容通过脚本来实现的。

```sh
#!/bin/bash

i=1
while [ $i -le 9 ] 
do
	echo name$i
	i=$[$i + 1]
done
```

但是仅仅获得变量名对我们是没多大帮助的，要是这些动态计算出来的变量名能够动态计算出值来就太好了。其实我们可以通过<code>eval</code>命令来实现我们的想法，

```sh
#!/bin/bash

name1=larry01
name2=larry02
name3=larry03
name4=larry04
name5=larry05
name6=larry06
name7=larry07
name8=larry08
name9=larry09

i=1
while [ $i -le 9 ] 
do
	name=$(eval echo \${name$i})
	echo $name wants to sleep.
	i=$[$i + 1]
done

```

是不是很好玩！

## WIN系统
同样的需求在WIN系统该怎么实现？  
我们可以使用DOS命令，虽然比起shell命令DOS弱爆了，但是它还是能够完成任务的。

```bat
set name1=larry01
set name2=larry02
set name3=larry03
set name4=larry04
set name5=larry05
set name6=larry06
set name7=larry07
set name8=larry08
set name9=larry09

SETLOCAL ENABLEDELAYEDEXPANSION
FOR /L %%i in (1, 1, 9) do echo !name%%i! wants to sleep.
ENDLOCAL
```

可以看出DOS里面使用两个感叹号括住对应变量组合就可以计算出相应的变量值，但是有个前提：要先设置<code>SETLOCAL ENABLEDELAYEDEXPANSION</code>。待处理结束后再运行<code>ENDLOCAL</code>，否则后面含有感叹号的地方都不能正常显示。
