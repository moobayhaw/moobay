---
layout: post
title: "使用Excel导出XML格式文件"
date: 2012-11-08 10:08
comments: true
categories: 
---
生成XML的方式有很多：如果你是程序员，那么你可以通过变成快速生成你想要的XML格式；如果你是其他用户，你可能会使用记事本等文本编辑工具来手动写XML。这些都不是问题，但是数据量很大的时候，如果你不是程序猿会不会郁闷？<!--more-->本文介绍了一种方法不借助编程也可以高效、快速地生成XML配置文件。在看这篇文章之前，我先假定了你们都会用微软Office工具Excel，其实不会问题也不是很大:-D  

假设现在有个任务，要生成一个班的学生XML配置，这个配置包含学生的学号、姓名、性别、年龄、出生年月信息，那么现在有两种XML格式可以选择：  

```xml
<student id="" name="" sex="" age="" birth=""/>

```

```xml
<student>
	<id></id>
	<name></name>
	<sex></sex>
	<age></age>
	<birth></birth>
</student>

```

可以很容易发现第一种描述更简洁，看起来有点小清新的感觉，我们现在就以第一种格式来介绍生成XML的方法。不过你真的很喜欢第二种方式，看到最后你惊喜地发现，我的方法也可解决你的问题。


现在回归正题，如果用Excel去录入这些数据，大家很可能是这样来做数据，这样做很容易理解，并且容易批量拖拽处理。![](/assets/images/export-xml-from-excel/fig01.png)
到这里我们可以发现，其实Excel可以完全把我们的数据描述清楚，那么如果Excel能把这些数据转成XML岂不是很好？是的，Excel可以做这些，但是Excel不知道你想要的XML格式，所以要给Excel定一个规则让它直到我们想要什么字段、输出什么格式。现在有两种方法给Excel指定输出规则：简单XML映射、SchemaXML映射，下面分别来做介绍。  

## 简单XML映射
对于上面的数据我们用下面这段XML来描述，然后保存为rule.xml文件。

```xml
<data>
	<student id="" name="" sex="" age="" birth=""/>
	<student id="" name="" sex="" age="" birth=""/>
</data>

```

<code>data</code>是输出XML的根节点，<code>student</code>是单个学生信息节点，这两个字段限定了XML输出的节点名称，<code>&lt;student&gt;&lt;/student&gt;</code>数据描述了单条XML节点属性的字段名称。这样整个输出规则就定制好了，是不是非常简单？可能会疑惑为什么有两条重复的<code>student</code>数据，这里我先不透露，大家可以看完这篇文章后自己试试看看什么效果。

接下来我们把这个XML规则导入Excel，进入“**文件**->**选项**->**自定义功能区**”，右边栏里面找到**开发工具**，确保其前面的复选框被勾上，并且确保**XML**前面的复选框也被勾上，如下图

![](/assets/images/export-xml-from-excel/fig02.png)

然后返回主界面，在选项卡区域找到**开发工具**，点击**源**按钮，这时Excel会出现一个左边栏，这时导入XML规则的入口操作界面，

![](/assets/images/export-xml-from-excel/fig03.png)

点击“**XML 映射...**->**添加**”，把之前保存的**rule.xml**文件导入进来，这时右边栏会出现一个树形的属性列表，列表字段就是**rule.xml**规则里面定义的字段，这些自动都是可以拖拽的：把<code>id</code>拖拽到<code>学号</code>栏，一次类推，把所有的字段都映射到Excel的数据栏。
![](/assets/images/export-xml-from-excel/fig04.png)

这样就完成了所有的规则定义以及映射工作，接下来就是高潮来临的时刻：**输出XML配置**。这一步非常简单，只需要点击一个按钮“**开发者工具**->**导出**”，然后选择你要输出的文件位置保存即可。

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<data>
	<student id="5325035" name="name::01" sex="0" major="21" birth="33349"/>
	<student id="5325036" name="name::02" sex="1" major="22" birth="33208"/>
	<student id="5325037" name="name::03" sex="0" major="20" birth="33744"/>
	<student id="5325038" name="name::04" sex="0" major="20" birth="33914"/>
	<student id="5325039" name="name::05" sex="0" major="21" birth="33398"/>
	<student id="5325040" name="name::06" sex="1" major="21" birth="33403"/>
	<student id="5325041" name="name::07" sex="1" major="21" birth="33366"/>
	<student id="5325042" name="name::08" sex="0" major="23" birth="32712"/>
	<student id="5325043" name="name::09" sex="1" major="21" birth="33536"/>
	<student id="5325044" name="name::10" sex="1" major="20" birth="33672"/>
	<student id="5325045" name="name::11" sex="1" major="21" birth="33397"/>
</data>

```

整个操作是不是很简单？结果是不是很完美？！！细心的同学可能发现了出生日期导出的好像有点奇怪，其实不必诧异：这时Excel表示日期的原始数据。但是跟想象的结果好像有点出入，你是否想对输出的格式作进一步的限制？如果是，那么下一个章节是你需要关注的。

## SchemaXML映射

其实我对Schema XML的了解也不太多，有一点我是可以确认的：**SchemaXML是一个XML内容的规则，它描述了XML有什么样的内容，并限定了XML内容有什么格式；简单说它是一个XML的语法规范**。为什么这样说呢？因为实际上我们为XML定义的没一个字段都是有其含义的，比如**年龄**字段只可能是一个整数，不可能是其他字符串之类的值；而**出生日期**就是一个日期格式，以此类推。这样你就会有点豁然开朗的感觉，是的，SchemaXML可以描述任意XML内容。到这里，我们可以用SchemaXML做Excel导出XML的规则就一点不奇怪了，应该说用它就最合适不过了！SchemaXML其实也是一个XML文件，但是写起来相对有点门槛。如果为上面的数据写一个SchemaXML规则，那么就应该类似下面这样：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified">
    <xs:element name="data">
        <xs:complexType>
            <xs:sequence>
                <xs:element maxOccurs="unbounded" name="student">
                    <xs:complexType>
                        <xs:attribute name="id" type="xs:integer" use="required"/>
                        <xs:attribute name="name" type="xs:string" use="required"/>
                        <xs:attribute name="sex" type="xs:integer" use="required"/>
                        <xs:attribute name="age" type="xs:integer" use="required"/>
                        <xs:attribute name="birth" type="xs:date" use="required"/>
                    </xs:complexType>
                </xs:element>
            </xs:sequence>
        </xs:complexType>
    </xs:element>
</xs:schema>

```

具体SchemaXML里面的这些字段表示什么意思，这就超出了本文介绍的范围，有兴趣的同学可以Google一些相关资料来学习一下。除了学习资料，也可以使用现成的可视化工具来生成配置，比如OxygenXML、LiquidXML等。下面是Oxygen可视化编辑工具的界面
![](/assets/images/export-xml-from-excel/fig05.png)
这里就不对这些工具做过多的介绍，有兴趣的同学可以下来体验下。  

把上面这段SchemaXML规则保存为**rule.xsd**文件，采用导入**rule.xml**的方式导入rule.xsd文件，导入后也会生成类似的树形字段列表，然后拖拽映射到相应的Excel数据列上，点击导出即可。

```xml
<data>
	<student id="5325035" name="name::01" sex="0" age="21" birth="1991-04-21"/>
	<student id="5325036" name="name::02" sex="1" age="22" birth="1990-12-01"/>
	<student id="5325037" name="name::03" sex="0" age="20" birth="1992-05-20"/>
	<student id="5325038" name="name::04" sex="0" age="20" birth="1992-11-06"/>
	<student id="5325039" name="name::05" sex="0" age="21" birth="1991-06-09"/>
	<student id="5325040" name="name::06" sex="1" age="21" birth="1991-06-14"/>
	<student id="5325041" name="name::07" sex="1" age="21" birth="1991-05-08"/>
	<student id="5325042" name="name::08" sex="0" age="23" birth="1989-07-23"/>
	<student id="5325043" name="name::09" sex="1" age="21" birth="1991-10-25"/>
	<student id="5325044" name="name::10" sex="1" age="20" birth="1992-03-09"/>
	<student id="5325045" name="name::11" sex="1" age="21" birth="1991-06-08"/>
</data>

```

这是使用SchemaXML导出的配置结果，在日期字段里面月份、日期如果是一位数字的前面会自动补0占位，看起来非常工整。

## 每个字段单独输出XML节点
这中方式映射与字段输出属性模式是非常类似，区别只是规则定义格式的差别，如果使用简单XML做规则，那么就应该是这样

```xml
<data>
	<student>
		<id></id>
		<name></name>
		<sex></sex>
		<age></age>
		<birth></birth>
	</student>
	<student>
		<id></id>
		<name></name>
		<sex></sex>
		<age></age>
		<birth></birth>
	</student>
</data>

```

这里的规则与上面类似，也是重复两段<code>student</code>，这个是必须的，原理也同上面类似。

如果使用SchemaXML，那么规则应该这样

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified">
    <xs:element name="data">
        <xs:complexType>
            <xs:sequence>
                <xs:element maxOccurs="unbounded" name="student">
                    <xs:complexType>
                        <xs:sequence>
                            <xs:element name="id" type="xs:integer"/>
                            <xs:element name="name" type="xs:string"/>
                            <xs:element name="sex" type="xs:integer"/>
                            <xs:element name="age" type="xs:integer"/>
                            <xs:element name="birth" type="xs:date"/>
                        </xs:sequence>
                    </xs:complexType>
                </xs:element>
            </xs:sequence>
        </xs:complexType>
    </xs:element>
</xs:schema>

```

相应的Oxygen可视化模型大致如下图

![](/assets/images/export-xml-from-excel/fig06.png)

## 注意事项

* 虽然XML、SchemaXML规则可以描述很复杂的XML内容，但是Excel只支持最多二级的XML配置导出
* 使用XML做规则时，一定不能只使用一条数据来描述，否则生成的结果只有Excel标题栏文本，而不是数据本身


