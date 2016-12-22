---
layout: post
title: "awk使用教程"
date: 2016-12-05 19:13:55 +0800
comments: true
categories: shell MAC awk
---

# 入门
`awk`在文本处理脚本`shell`里很常用，它从管道`|`或文件中读取每一行，然后按照一定规则把每行自动分成多列，默认使用**空格**自动分列。在`awk`里面，**空格**可以是`空白字符`、`TAB制表符`。分列可以让`awk`脚本很方便地引用这些分隔开的值，`$1`表示第一列，`$2`表示第二列，等等以此类推，当然`$`后面的数字可以是个很大的值，比如`$1024`。另外，在`awk`中使用`$0`表示整行,`$NF`表示最后一列。

先来看个示例

```bash
echo 'this seems like a pretty nice example' | awk '{print $1}'
```

`$1`是**this**，`$2`是**seems**，`$7`和`$NF`是**example**，在该例子中使用**空格**分隔成7列，完整语法是这样的

```bash
echo 'this seems like a pretty nice example' | awk -F' ' '{print $1}'
```
可以看到`awk`多了一个`-F`参数，通过该参数可以设置各种分隔符，**单字符**、**多字符**都能很好的支持，同样的示例我可以添加`-F' like '`这样的多字符分隔符，可以得到结果**this seems**.

```bash
echo 'this seems like a pretty nice example' | awk -F' like ' '{print $1}'
```

<!--more-->
除了`NF`，`awk`还有一些其他内置变量，见下表

|内置变量|变量描述|
|------:|:------------|
|CONVFMT|转换数字使用的格式，默认为：%.6g|
|FS     |分列使用的正则表达式，可以通过-Ffs来设置|
|NF     |当前行分隔后的列数|
|NR     |当前行的序号|
|FNR    |当前文件中当前行的序号|
|FILENAME |当前输入文件名|
|RS     |行分隔符，默认为换行符\\n|
|OFS    |输出列间隔符，默认为空格|
|ORS    |输出行分隔符，默认为换行符\\n|
|OFMT   |数字的输出格式，默认为：%.6g|
|SUBSEP |下标分隔符，默认为：\\034(双引号)，比如foo["A","B"]，实际通过foo["A\034B"]来访问|
|ARGC   |awk参数数量，可赋值|
|ARGV   |awk参数列表，可赋值，0..ARGC-1，非0索引表示输入文件名|
|ENVIRON |环境变量列表，通过下标变量名(ENVIRON[变量名])来访问，环境变量var=value，通过ENVIRON[var]=value方式存储|

`&#034;`=&#034;

```bash
awk 'BEGIN{for(i=0;i<ARGC;i++)print i,ARGV[i]}' *.cs
```
<pre>
0 awk
1 ArenaServer.cs
2 LogicServer.cs
3 Server.cs
4 Utils.cs
</pre>

```bash
awk 'BEGIN{for(i in ENVIRON)print i " = " ENVIRON[i]}' *.cs
```
<pre>
DISPLAY = /private/tmp/com.apple.launchd.eKuWVlBt3M/org.macosforge.xquartz:0
Apple_PubSub_Socket_Render = /private/tmp/com.apple.launchd.grOg8OMLVo/Render
OLDPWD = /Users/larryhou/Documents/next/trunk
LOGNAME = larryhou
XPC_SERVICE_NAME = 0
JAVA_HOME = /Library/Java/JavaVirtualMachines/jdk1.8.0_45.jdk/Contents/Home
USER = larryhou
PATH = /usr/local/sbin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/Applications/Server.app/Contents/ServerRoot/usr/bin:/Applications/Server.app/Contents/ServerRoot/usr/sbin:/Applications/Wireshark.app/Contents/MacOS
__CF_USER_TEXT_ENCODING = 0x1F5:0x0:0x0
TERM_SESSION_ID = A39F566B-15B3-4423-9716-491208377960
TERM_PROGRAM_VERSION = 388
TERM = xterm-256color
SHLVL = 1
TMPDIR = /var/folders/gb/2wdjrlq53fbdf0xbs145cv2m0000gn/T/
SECURITYSESSIONID = 186a6
HOME = /Users/larryhou
SHELL = /bin/bash
TERM_PROGRAM = Apple_Terminal
LC_CTYPE = UTF-8
_ = /usr/bin/awk
PWD = /Users/larryhou/Documents/next/trunk/TheNextMOBA/Assets/Scripts/Service/Network
SSH_AUTH_SOCK = /private/tmp/com.apple.launchd.eDbmxn43sK/Listeners
XPC_FLAGS = 0x0
</pre>

```bash
awk '{print FILENAME ":" sprintf("%02d",NR),$0}' Server.cs.meta  
```
<pre>
Server.cs.meta:01 fileFormatVersion: 2
Server.cs.meta:02 guid: c265c6b6a7a784f3bb1417ce67e9df3e
Server.cs.meta:03 timeCreated: 1456236560
Server.cs.meta:04 licenseType: Free
Server.cs.meta:05 MonoImporter:
Server.cs.meta:06   serializedVersion: 2
Server.cs.meta:07   defaultReferences: []
Server.cs.meta:08   executionOrder: 0
Server.cs.meta:09   icon: {instanceID: 0}
Server.cs.meta:10   userData: 
Server.cs.meta:11   assetBundleName: 
Server.cs.meta:12   assetBundleVariant: 
</pre>

# 正则表达式
上面通过分隔符分列是`awk`的基本功能，`awk`还可以和[正则表达式结合][regex]相结合做出复杂的效果

	1048592             |FowTexture:Init ()|Color[]             
	262160              |AStar:.ctor (Map)|Int32[]             
	196684              |TheNextMoba.Module.Arena.ArenaResourceLoader:OnItemLoadSuccess (string,object)|Byte[]              
	192016              |Morefun.LockStep.LockstepProfiler/Stat:CreateFrameContext ()|FrameContext[]      
	131088              |SignedDistanceField:.ctor (Map,int)|Int16[]             
	81936               |GameObjectUtil:Init ()|String[]            
	65568               |TheNext.Moba.Logic.FOWManager:Init ()|Byte[,]             
	65568               |TheNext.Moba.Logic.FOWManager:Init ()|Byte[,]             
	65568               |TheNext.Moba.Logic.FOWSystem:Init (TheNext.Moba.Logic.EnmTeamID,TheNext.Moba.Logic.IFOWSystem)|Byte[,]             
	65568               |TheNext.Moba.Logic.FOWSystem:Init (TheNext.Moba.Logic.EnmTeamID,TheNext.Moba.Logic.IFOWSystem)|Byte[,] 

```bash
awk '/Init/ {print $0}'
```
	
	1048592             |FowTexture:Init ()|Color[]             
	81936               |GameObjectUtil:Init ()|String[]            
	65568               |TheNext.Moba.Logic.FOWManager:Init ()|Byte[,]             
	65568               |TheNext.Moba.Logic.FOWManager:Init ()|Byte[,]             
	65568               |TheNext.Moba.Logic.FOWSystem:Init (TheNext.Moba.Logic.EnmTeamID,TheNext.Moba.Logic.IFOWSystem)|Byte[,]             
	65568               |TheNext.Moba.Logic.FOWSystem:Init (TheNext.Moba.Logic.EnmTeamID,TheNext.Moba.Logic.IFOWSystem)|Byte[,] 

通过`awk '/Init/'`这个简单的例子可以知道，在双斜杠`'//'`中间可以添加正则表达式，正则表达式的作用过滤出匹配的行 然后再执行花括号里面`{}`逻辑，该示例中正则对整行进行匹配操作，也可以对某一列执行匹配

```bash
awk -F'|' '$2 ~ /Init/ {print $0}'
```
脚本用`|`分隔符分列，把正则表达式应用到第二列进行匹配

```bash
awk -F'|' '$2 ~ /Init/ {print $1,$3}'
```

	1048592              Color[]             
	81936                String[]            
	65568                Byte[,]             
	65568                Byte[,]             
	65568                Byte[,]             
	65568                Byte[,] 
	
# 表达式
`awk`也支持常见的流程控制语句`if...else`、`for`、`for...in`、`while`、`do...while`

`for`循环和三元表达式

```bash
echo 192.168.0.105 | awk -F'.' '{for(i=1;i<=NF;i++)printf "%02X" (i<NF?":":""),$i}'
```

```bash
echo 192.168.0.105 | awk -F'.' '{for(i=1;i<=NF;i++)
				printf "%02X" (i<NF?":":""),$i }'
```

`for`循环和`if`表达式

```bash
echo 192.168.0.105 | awk -F'.' '{for(i=1;i<=NF;i++){printf "%02X",$i;if(i<NF)printf ":"}}'
```

```bash
echo 192.168.0.105 | awk -F'.' '{for(i=1;i<=NF;i++)
{
	printf "%02X",$i;
	if(i<NF)printf ":"
}}'
```

<pre>
C0:A8:00:69
</pre>

`for...in`和`split`

```bash
echo 192.168.0.105 | awk -F'.' '{split($0,a);for(i in a) printf "%d:%02X ",i,a[i]}'
```

`while`

```bash
head -n 5 mono.txt | awk -F'|' '{n=1;while(n++<=NF)print $n}'
```

<pre>
FowTexture:Init ()
Color[]             

AStar:.ctor (Map)
Int32[]             

TheNextMoba.Module.Arena.ArenaResourceLoader:OnItemLoadSuccess (string,object)
Byte[]              

Morefun.LockStep.LockstepProfiler/Stat:CreateFrameContext ()
FrameContext[]      

SignedDistanceField:.ctor (Map,int)
Int16[]         
</pre>
其他各种表达式

```bash
if( expression ) statement [ else statement ]
while( expression ) statement
for( expression ; expression ; expression ) statement
for( var in array ) statement
do statement while( expression )
break
continue
{ [ statement ... ] }
expression	      # commonly var = expression
print [ expression-list ] [ > expression ]
printf format [ , expression-list ] [ > expression ]
return [ expression ]
next		      # skip remaining patterns on this input line
nextfile		      # skip rest of this file, open next, start at top
delete array[ expression ]# delete an array element
delete array	      # delete all elements of array
exit [ expression ]     # exit immediately; status is expression
```

可以在表达式中使用的`awk`内置函数

|函数名|函数描述|
|:----|:----|
|length|字符串长度|
|rand|生成(0<=x<1)范围的随机数|
|srand|设置随机种子并返回之前的随机种子|
|int|截尾转换成整数|
|substr(s,m,n)|返回字符串s自m位置(索引从1开始)开始的n个字符|
|index(s,t)|如果字符串s中包含t，则返回t在字符串中出现的位置(从1开始)，否则返回0|
|match(s,r)|返回字符串s匹配正则表达式r的位置(从1开始)|
|split(s,a,fs)|把字符串s通过正则fs分隔成数组a，并返回数组a长度n，如果fs未设置，则使用FS分隔符|
|sub(r,t,s)|在字符串s中，使用正则表达式r首次匹配的位置替换成t，如果s未设置，则使用$0|
|gsub(r,t,s)|与sub(r,t,s)参数相同，不过会把匹配的字符串全部替换，并返回替换的次数|
|sprintf(fmt,expr,...)|使用与printf相同的样式fmt格式表达式列表|
|system(cmd)|执行cmd并返回退出状态码|
|tolower(str)|把字符串str转换成小写|
|toupper(str)|把字符串str转换成大写|

以及各种运算符

|运算符|描述|
|--:|:---|
|=,+=,-=,*=,/=,%=,^=,**=|赋值|
|?:|C条件表达式，三元表达式|
|\|\||逻辑或|
|&&|逻辑与|
|~,~!|匹配正则表达式和不匹配正则表达式|
|<,<=,>,>=,!=,==|关系运算符|
|空格|连接|
|+,-|加，减|
|*,/,%|乘，除与求余|
|+,-,!|一元加，减和逻辑非|
|^,***|求幂|
|++,\-\-|增加或减少，作为前缀或后缀|
|$|字段引用|
|in|数组成员|

# 综合示例

下面这个示例使用`awk`做了一个相对比较复杂的事情：把第二列相同的行归为一类，并把相同类的第一列数值相加求和，并输出汇总结果

```bash
#!/bin/bash

export LC_NUMERIC="en_US.UTF-8"
cat data.txt | awk 'BEGIN{sum=0;type="";prev=""}{
	if(type=="" || type!=$2)
	{
		if(type!="")
		{
			printf "%10'\''.d %s %s\n", sum, prev,"#" NR
		}
		
		type=$2
		prev=sprintf("%9'\''.d %s %s",$1,$2,$3)
		sum=0
	}
	
	sum+=$1
	
	# if(NR>10000) exit
}'
```

<pre>
   656,632        96 System.Collections.Generic.List`1&lt;int&gt;:.ctor(System.Collections.Generic.IEnumerable`1&lt;int&gt;) Int32[] #315133
   859,648        64 System.Collections.Generic.Dictionary`2&lt;object,object&gt;:InitArrays(int) WeakObjectWrapper[] #347584
   964,608       976 System.Collections.Generic.List`1&lt;UnityEngine.UIVertex&gt;:.ctor(int) UIVertex[] #333434
 1,048,592 1,048,592 FowTexture:Init() Color[] #687192
 1,065,568       464 System.Collections.Generic.Dictionary`2&lt;int16,Morefun.LockStep.LList`1&lt;System.Delegate&gt;&gt;:InitArrays(int) Link[] #351569
 1,132,052       274 System.MonoType:get_AssemblyQualifiedName() String #243980
 1,181,198        48 UnityEngine.Object:Internal_InstantiateSingle(UnityEngine.Object,UnityEngine.Vector3,UnityEngine.Quaternion) VisualsTowerAnim #124684
 1,187,060        64 System.Reflection.MonoCMethod:Invoke(object,System.Reflection.BindingFlags,System.Reflection.Binder,object[],System.Globalization.CultureInfo) WaypointFollower #239548
 1,790,736        98 string:Concat(string,string) String #62269
 2,219,368        16 HutongGames.PlayMaker.ActionData:Copy() MonoType #671794
</pre>


如果在`awk`操作里面使用单引号`'`则需要[转义][link_02]，可以通过`'\''`来实现，上面这个示例中使用了两次单引号`'`。另外，使用了`printf`对数值进行[千分位格式化][link_03]，脚本使用的数据源文件通过<a class='.blog-link' href='/assets/awk-tutorials/data.txt.7z' title='下载数据文件：data.txt'>链接</a>下载。


延伸阅读：<br/>
[Awk User's Guide:Very Simple][gnu_01]<br/>
[Awk User's Guide:Fields][gnu_02]<br/>
[awk用法][link_01]<br/>

[regex]: https://www.gnu.org/software/gawk/manual/html_node/Regexp.html#Regexp  "正则表达式"
[gnu_01]: https://www.gnu.org/software/gawk/manual/html_node/Very-Simple.html "Awk User's Guide:Very Simple"
[gnu_02]: https://www.gnu.org/software/gawk/manual/html_node/Fields.html "Awk User's Guide:Fields"
[link_01]: http://www.cnblogs.com/emanlee/p/3327576.html "awk用法"
[link_02]: http://stackoverflow.com/questions/9899001/how-to-escape-single-quote-in-awk-inside-printf "awk单引号转义"
[link_03]: http://stackoverflow.com/questions/14668094/add-commas-to-numeric-strings-in-unix "printf输出千分位格式数值"




