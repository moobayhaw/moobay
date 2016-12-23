---
layout: post
title: "Interface, Protocol, Category and Extension in Objective-C"
date: 2012-11-21 21:12
comments: true
categories: 
---

假如你要学习Objective-C，那么你需要搞清楚interface、protocol、category以及extension这几个概念，它们在代码的出现频率非常高，也非常有用。本文将对这几个概念一一来说明，并介绍各自的使用方法。<!--more-->

## Interface
interface在Objective-C里面充当头文件作用，在Xcode里面interface是以*.h为后缀的文件，在里面可以定义类文件所需的成员变量、getter/setter以及函数方法，但是这些声明要放到<code>@interface ... @end</code>指令中间。

```objc
#import <Foundation/Foundation.h>

@interface Mobile : NSObject
{
    NSString *name;
    
    @private
    NSString *model;
    
    @protected
    NSString *sn;
    
    @public
    NSString *producer;
}

@property NSString *photoNumber;
@property NSString *mail;

+(Mobile *) create;

-(void) call;

@end

```

在这个头文件里面定义了四个成员变量name、model、sn、producer，其中name、model是私有变量，sn是protected变量，producer为public变量；photoNumber、mail是getter/setter函数;前面有个减号的call是一个实例方法，前面有个加号的create是一个类方法，与Actionscript的public静态方法类似。  

interface头文件声明的方法需要在<code>@implementation ... @end</code>指令里面实现，如下

```objc
#import "Mobile.h"

@implementation Mobile

@synthesize photoNumber;
@synthesize mail;

-(id)init
{
    self = [super init];
    if(self)
    {
        self->name = @"iPhone";
        self->model = @"A1332";
        self->producer = @"Apple Inc.";
    }
    
    return self;
}

+(Mobile *)create
{
    return [[Mobile alloc] init];
}

-(void)call
{
    NSLog(@"Call %@", self.photoNumber);
}

@end

```

成员变量可以通过<code>self-></code>来访问，不管该变量是private还是protected、public。相比之下，如果要访问getter/setter方法<code>photoNumber</code>，那么可以有三种方式：

```objc
NSLog(@"Call %@", self.photoNumber);
NSLog(@"Call %@", self->photoNumber);
NSLog(@"Call %@", [self photoNumber]);
```

<code>call</code>是实例方法，我们可以构造一个<code>Mobile</code>实例来调用，如下

```objc
Mobile *mobile = [[Mobile alloc] init];
mobile.photoNumber = @"15988886039";
[mobile call];
```

而<code>create</code>是类方法，可以通过类名直接访问，如下

```objc
Mobile *mobile = [Mobile create];
mobile.photoNumber = @"15988886039";
[mobile call];
```

## Protocol
protocol也是一个*.h文件，通过<code>@protocol ... @end</code>来声明接口(函数方法或者getter/setter)，跟interface有点类似。它有什么用呢？

* 声明一个隐藏类的调用接口
* 在非继承关系的类之间可以做类型转换

这个与Actionscript的interface文件作用是一致的，任何类都可以去实现这个protocol接口，然后具有protocol声明的类型。

```objc
#import <Foundation/Foundation.h>

@protocol IMobile <NSObject>

@property NSString *photoNumber;

-(void) call;

@required
-(void) playMusic;

@optional
-(void) restart;

@end

```

<code>IMobile</code>接口声明了两个必须实现的接口<code>call、playMusic</code>，一个可选接口<code>restart</code>，也是restart可以选择不实现。实现这个protocol接口，可以在interface文件中添加一个尖括号来声明，如下

```objc
#import <Foundation/Foundation.h>
#import "IMobile.h"

@interface Mobile : NSObject <IMobile>
{
    NSString *name;
    
    @private
    NSString *model;
    
    @protected
    NSString *sn;
    
    @public
    NSString *producer;
}

@property NSString *photoNumber;
@property NSString *mail;

+(Mobile *) create;

-(void) call;

@end

```

然后通过<code>@implementation ... @end</code>来实现，<code>IMobile</code>声明了getter/setter方法<code>photoNumber</code>以及<code>call、playMusic</code>两个实例方法，由于<code>photoNumber、call</code>已经被<code>Mobile</code>实现，所以只需要实现<code>playMusic</code>即可。如下

```objc
#import "Mobile.h"

@implementation Mobile

@synthesize photoNumber;
@synthesize mail;

-(id)init
{
    self = [super init];
    if(self)
    {
        self->name = @"iPhone";
        self->model = @"A1332";
        self->producer = @"Apple Inc.";
    }
    
    return self;
}

+(Mobile *)create
{
    return [[Mobile alloc] init];
}

-(void)call
{    
    NSLog(@"Call %@", [self photoNumber]);
}

-(void)playMusic
{
    NSLog(@"Play music.");
}

@end

```

protocol接口实现后，可以这样来使用

```objc
id <IMobile> mobile = [[Mobile alloc] init];
[mobile setPhotoNumber:@"15988886039"];
[mobile playMusic];
[mobile call];

```

或者

```objc
Mobile <IMobile> *mobile = [[Mobile alloc] init];
mobile.photoNumber = @"15988886039";
        
[mobile setPhotoNumber:@"15988886039"];
[mobile playMusic];
[mobile call];

```

上面说到<code>restart</code>为可选接口，有可能没有被实现，在没有实现的情况下去调用就会报错，对于这种接口就需要在使用前检测一下是否可用，如下

```objc
SEL restart = @selector(restart:);
if([mobile respondsToSelector:restart])
{
    [mobile restart];
}
else
{
    NSLog(@"restart is not complement.");
}

```

注意：上面提到<code>@required</code>指令标记的方法必须实现，不然会出现编译警告

![](/assets/images/interface-protocol-and-category/fig01.png)
## Category
category可以对一个类文件添加方法，哪怕不知道这个类的代码，只要有这个类的interface头文件即可。这个功能有什么用呢？举个简单例子就可以很容易理解。

```objc
#import <Foundation/Foundation.h>

@interface NSObject (Run)
-(void) run;
@end

```

```objc
#import "NSObject+Run.h"

@implementation NSObject (Run)
-(void)run
{
    NSLog(@"Run! ::%@", [self className]);
}
@end

```

category使用实例

```objc
NSString *name = @"larryhou";
[name run];

Mobile <IMobile> *mobile = [[Mobile alloc] init];
mobile.photoNumber = @"15988886039";
[mobile playMusic];
[mobile call];
[mobile run];

```

有没有发现什么情况？  
我在NSObject+Run.h头文件定义了一个<code>run</code>的方法，然后在NSObject+Run.m文件中实现<code>run</code>方法，然后再<code>#import "NSObject+Run.h"</code>，然后奇迹发生了：所有继承了NSObject的类都好像有了<code>run</code>这个方法一样！！！是的，没错就是这样！是不是很酷！！  

细心的同学可能已经发现，定义category需要写一个interface文件，<code>@interface</code>指令后面紧跟**被扩展类名**，然后写在圆括号里面写入category名称，可以为任意字符串。在实现categor的interface的时候也要注意：<code>@implementation</code>指令后面紧跟**被扩展类名**，然后在圆括号里面写入category名称，与interface声明保持一致，其他与实现普通类的interface方式一样。

这样再不修改源代码的情况下，给一个类添加方法还可以有另外一个用途：**把类逻辑拆分到不同的文件里面，也就是说一个类可以有多个文件组成**。

前面讲到<code>IMobile</code>有一个<code>@optional</code>标记的方法<code>restart</code>，现在我们把<code>restart</code>声明去掉，通过category方式来扩展<code>Mobile</code>的功能。

<code>Mobile</code>扩展头文件

```objc
#import "Mobile.h"

@interface Mobile (Restart)

-(void) restart;

@end

```

<code>Mobile</code>扩展实现

```objc
#import "Mobile+Restart.h"

@implementation Mobile (Restart)

-(void)restart
{
    NSLog(@"Restart mobile phone.");
}

@end

```

<code>Mobile</code>扩展使用实例

```objc
Mobile *mobile = [[Mobile alloc] init];
mobile.photoNumber = @"15988886039";
[mobile playMusic];
[mobile restart];
[mobile call];

```

这个功能有点像代码注入，感觉非常棒！\\(&#94;o&#94;)/~

## Extension
我在Mobile.m文件里面添加如下一段代码

```objc
@interface Mobile ()

@property NSInteger *size;

-(void) sleep;

@end

```

然后在Mobile.m文件里面实现这些声明

```objc
#import "Mobile.h"

@interface Mobile ()

@property NSInteger *size;

-(void) sleep;

@end

@implementation Mobile

@synthesize photoNumber;
@synthesize size;
@synthesize mail;

-(id)init
{
    self = [super init];
    if(self)
    {
        self->name = @"iPhone";
        self->model = @"A1332";
        self->producer = @"Apple Inc.";
    }
    
    return self;
}

+(Mobile *)create
{
    return [[Mobile alloc] init];
}

-(void)call
{    
    NSLog(@"Call %@", self.photoNumber);
    NSLog(@"Call %@", self->photoNumber);
    NSLog(@"Call %@", [self photoNumber]);
}

-(void)playMusic
{
    NSLog(@"Play music.");
    
    [self sleep];
}

-(void)sleep
{
    NSLog(@"Sleep.");
}

@end

```

然后调用<code>sleep、size</code>看看发生了什么 
	 
![](/assets/images/interface-protocol-and-category/fig02.png)

报错了！这是为什么？  

这段接口声明看起来好像是跟Mobile.h文件是重复的，其实是对Mobile.h的补充，但是<code>@interface Mobile ()</code>有个没有任何内容的圆括号，那么在这种补充模式下声明的接口是私有的不能在类文件意外调用，在类文件内部是可以使用的，如下

```objc
-(void)playMusic
{
    NSLog(@"Play music.");
    
    [self sleep];
}

```

到这里interface、protocol、category和extension这四个概念都已经介绍完了，搞清楚了再去学习Objective-C基本上就是如鱼得水了！GOOD LUCK!
