---
title : BitmapData.draw() 和 DisplayObject.blendMode
layout: post
comments: true
---

<code>BitmapData.draw()</code>如果遇到<code>DisplayObject.blendMode</code>有时候效果会异常，这里有个陷阱...oops
   
```as3
var container:Sprite = new [some DisplayObjectContainer class]();
var childA:Sprite = new [some DisplayObject class]();
var childB:Sprite = new [some DisplayObject class]();

childA.blendMode = BlendMode.ADD;

container.addChild(childA);
container.addChild(childB);

var data:BitmapData = new BitmapData(container.width, container.height, true, 0);
data.draw(container);
```
  
如上面这段代码，看起来没有什么问题，但是如果把<code>data</code>通过<code>Bitmap</code>显示出来，就会发现效果有些诡异，这是因为<code>BitmapData</code>在<code>draw</code>的时候，会自动忽略<code>childA.blendMode</code>属性，除非把<code>childA</code>的父级容器设置为<code>container.blendMode = BlendMode.LAYER</code>。
