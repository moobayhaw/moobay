---
layout: post
title: "自定义Xcode代码模板：Code Snippet"
date: 2012-11-26 09:40
comments: true
categories: 
---

Xcode强大的代码提示功能是有目共睹的，用过都知道，可是你是否想过添加一些自定义的代码提示模板？你是否想让if默认的左括号挪到下一行？还有for、while、switch等等……如果你有这方面的需求的话，这篇文章刚好可以帮到你！ <!--more-->  

在Xcode左下角有个花括号的页签，这里面存储了很多代码提示模板，如下图 

![](/assets/images/customize-xcode-snippet/fig01.png)
点击列表里面任一个项目都会弹出一个说明框，里面有个**Edit**和**Done**按钮，这很容易让人产生编辑的想法，其实这是个错觉：这些内置的代码模板是不可编辑的！你看到这里可能会有点着急，因为你可能想问那if左括号是不是不能挪到下一行了？！其实，你如果用过Apple的产品，那你应该会想到Apple不会这么SB，这些都是可以编辑的！下面我们介绍编辑Xcode内置代码模板。

## 编辑Xcode内置代码模板
Xcode的所有代码模板是用一个plist格式xml文件描述的，这文件存储在Xcode的安装目录：

>/Applications/Xcode.app/Contents/PlugIns/IDECodeSnippetLibrary.ideplugin/Contents/Resources/¬
>SystemCodeSnippets.codesnippets

可以用任意文本编辑器打开这个代码模板配置文件，这是一段if的代码模板的XML配置,

```xml
<dict>
    <key>IDECodeSnippetVersion</key>
    <integer>1</integer>
    <key>IDECodeSnippetCompletionPrefix</key>
    <string>if</string>
    <key>IDECodeSnippetContents</key>
    <string>if (&lt;#condition#&gt;) {
&lt;#statements#&gt;
}</string>
    <key>IDECodeSnippetIdentifier</key>
    <string>D70E6D11-0297-4BAB-88AA-86D5D5CBBC5D</string>
    <key>IDECodeSnippetLanguage</key>
    <string>Xcode.SourceCodeLanguage.C</string>
    <key>IDECodeSnippetSummary</key>
    <string>Used for executing code only when a certain condition is true.</string>
    <key>IDECodeSnippetTitle</key>
    <string>If Statement</string>
    <key>IDECodeSnippetCompletionScopes</key>
    <array>
        <string>CodeBlock</string>
    </array>
</dict>
```

其中`IDECodeSnippetContents`字段表示具体代码模板的内容，虽然Xcode不允许编辑内置的代码模板，但是Xcode允许用户自定义代码模板，并且每个模板有一个唯一标识符`IDECodeSnippetIdentifier`，其中if模板的唯一标记是D70E6D11-0297-4BAB-88AA-86D5D5CBBC5D，那么既然每个代码模板是唯一存在的，那么如果自定义的模板的唯一标识符与内置代码模板相同了会发生什么情况？覆盖！这是我们修改内置代码模板的关键入口，也是核心思想！  

到这里一切都变得很清晰了：我们只要自定义一个相同标识符的带模板就可以覆盖内置的代码模板。官方文档介绍了创建自定义模板的方法：在Xcode输入代码模板代码，然后拖拽到Xcode的模板栏，然后点击“**Edit**”就是可以做相关的修改，编辑完后点击“**Done**”就可以使用了，然后在下面这个目录可以找到模板配置文件。

>~/Library/Developer/Xcode/UserData/CodeSnippets/

如果你用上述方法添加了一个自定义模板，那么你在这个目录可能看到类似D70E6D11-0297-4BAB-88AA-86D5D5CBBC5D.codesnippet命名的文件，其实这个文件名就是模板的唯一标识符。其实我们并不想要这样的命名方式，因为不方便维护以及分享，虽然在Xcode里面可以很方便地编辑他们。庆幸的是这个文件名是可以修改的，你可以像这样用一些有含义的字符来修改命名。  
![](/assets/images/customize-xcode-snippet/fig02.png)

但是有个问题：Xcode会自动为我们的模板分配一个唯一标识符，这样就不能达到覆盖的目的。有些同学可能想到从SystemCodeSnippets.codesnippets配置里面查找相关的标识符，然后黏贴过来，这样确实可以达到目的。不过现在我给大家推荐另外一种方法。  

首先，到SystemCodeSnippets.codesnippets里面找到你要修改的模板配置，然后复制黏贴到如下这段配置中 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
	<!--code config here-->
</plist>
```

然后修改`IDECodeSnippetVersion`键值为2，暂时不确定为什么要这么修改，但是Xcode生成的自定义模板配置都是这个值，保持默认1也没多大问题，只是Xcode有时会自动用标识符替换你的自定义命名，所以用2是比较合适的。这样还没完，因为还要添加一个键值为true的字段`IDECodeSnippetUserSnippet`，该字段标记了模板是否为自定义的，如果是自定义的模板但是没有这个字段，你的Xcode启动后可能会崩溃...orz

```xml
<key>IDECodeSnippetUserSnippet</key>
<true/>
```

那么如果替换内置的if模板，那么完整的配置应该是这样的。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
	<dict>
		<key>IDECodeSnippetCompletionPrefix</key>
		<string>if</string>
		<key>IDECodeSnippetCompletionScopes</key>
		<array>
			<string>CodeBlock</string>
		</array>
		<key>IDECodeSnippetContents</key>
		<string>if (&lt;#condition#&gt;) 
{
    &lt;#statements#&gt;
}</string>
		<key>IDECodeSnippetIdentifier</key>
		<string>5163356F-D409-4EDA-B263-EA2E07A50B9C</string>
		<key>IDECodeSnippetLanguage</key>
		<string>Xcode.SourceCodeLanguage.C</string>
		<key>IDECodeSnippetSummary</key>
		<string>Used for executing code only when a certain condition is true.</string>
		<key>IDECodeSnippetTitle</key>
		<string>If Statement</string>
		<key>IDECodeSnippetUserSnippet</key>
		<true/>
		<key>IDECodeSnippetVersion</key>
		<integer>2</integer>
	</dict>
</plist>

```

你可能注意到类似`\&lt;#condition#\&gt;`这样的代码，它们是什么呢？你再写代码遇到代码提示的时候，可能遇到过蓝色泡泡一样的东西，可以用Tab键来切换，那么这个奇怪的字符就是用来添加蓝色泡泡的。因为这是XML配置，所以做了HTML转义；如果你在Xcode里面编辑模板并添加蓝色泡泡的话，直接输入`<#condition#>`即可，##符号之间可以为任意字符。

## 隐藏代码模板提示

有时候你可能想要隐藏某个内置的模板，这样的需求也是可以完成的。你只需要把`IDECodeSnippetCompletionPrefix`字段的键值留空既可以，如果你把如下这段配置保存到模板目录，那么你的Xcode将不再弹出`if`语句的代码提示。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
	<dict>
		<key>IDECodeSnippetCompletionPrefix</key>
		<string></string>
		<key>IDECodeSnippetCompletionScopes</key>
		<array>
			<string>CodeBlock</string>
		</array>
		<key>IDECodeSnippetContents</key>
		<string>if (&lt;#condition#&gt;) 
{
    &lt;#statements#&gt;
}</string>
		<key>IDECodeSnippetIdentifier</key>
		<string>5163356F-D409-4EDA-B263-EA2E07A50B9C</string>
		<key>IDECodeSnippetLanguage</key>
		<string>Xcode.SourceCodeLanguage.C</string>
		<key>IDECodeSnippetSummary</key>
		<string>Used for executing code only when a certain condition is true.</string>
		<key>IDECodeSnippetTitle</key>
		<string>If Statement</string>
		<key>IDECodeSnippetUserSnippet</key>
		<true/>
		<key>IDECodeSnippetVersion</key>
		<integer>2</integer>
	</dict>
</plist>

```

在Xcode里面输入`if`，就不见了`if statement`的提示

![](/assets/images/customize-xcode-snippet/fig03.png)

是不是很酷？！  

最后总结一下自定义的注意事项：

>* 自定义目录不能有相同标识符的模板，否则Xcode启动后会崩溃；
>* 自定义母的模板标识符可以跟系统默认模板标识符相同，可以达到覆盖效果；
>* 若要使用自定义模板覆盖系统模板，则必须有`DECodeSnippetUserSnippet`字段，否则Xcode启动后会崩溃;



