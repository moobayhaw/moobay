---
layout: post
title: "Convert SWF vector shape to SVG image"
date: 2015-09-05 02:06:44 +0800
categories: 
---

<a href="/assets/swf-svg/graph-01.svg" target="_blank"><img src="/assets/swf-svg/graph-01.svg"/></a>
<!--more-->
<a href="/assets/swf-svg/graph-02.svg" target="_blank"><img src="/assets/swf-svg/graph-02.svg"/></a>
<div class="blog-link">
	<a href="/assets/swf-svg/graph-03.svg" target="_blank" style="margin-right:10px">graph-03.svg</a>
	<a href="/assets/swf-svg/graph-04.svg" target="_blank" style="margin-right:10px">graph-04.svg</a>
	<a href="/assets/swf-svg/graph-05.svg" target="_blank" style="margin-right:10px">graph-05.svg</a>
	<a href="/assets/swf-svg/graph-06.svg" target="_blank" style="margin-right:10px">graph-06.svg</a>
	<a href="/assets/swf-svg/graph-07.svg" target="_blank" style="margin-right:10px">graph-07.svg</a>
	<a href="/assets/swf-svg/graph-08.svg" target="_blank" style="margin-right:10px">graph-08.svg</a>
	<a href="/assets/swf-svg/graph-09.svg" target="_blank" style="margin-right:10px">graph-09.svg</a>
	<a href="/assets/swf-svg/graph-10.svg" target="_blank" style="margin-right:10px">graph-10.svg</a>
</div>

``` html
<img src="/assets/swf-svg/graph-01.svg"></img>
```

``` html
<embed src="/assets/swf-svg/graph-01.svg" width="300" height="100" type="image/svg+xml"
    pluginspage="http://www.adobe.com/svg/viewer/install/" />
```

``` html
<object data="/assets/swf-svg/graph-01.svg" width="300" height="100" type="image/svg+xml"
    codebase="http://www.adobe.com/svg/viewer/install/" />
```

``` html
<iframe src="/assets/swf-svg/graph-01.svg" width="300" height="100"></iframe>
```

``` html
<html>
    <body>
        <svg width="300" height="100" version="1.1" >
            <circle cx="100" cy="50" r="40" stroke="black" stroke-width="2" fill="red" />
            Sorry, your browser does not support inline SVG.
        </svg>
    </body>
</html>
```

{% gist e0131d953ef1d407d72a %}