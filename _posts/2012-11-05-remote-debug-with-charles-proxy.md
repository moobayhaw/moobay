---
layout: post
title: "使用charles远程调试iOS移动应用"
date: 2012-11-05 16:38
comments: true
categories: 
---

做iOS移动应用很多开发者会喜欢抓网络发包、回包来联调服务端借口以及定位其他网络问题。如果在Windows系统可以使用fiddler来做iOS的远程代理，只要fiddler所在系统与iOS设备同时连上同一个局域网即可。但是在OSX系统上没有fiddler，相信做iOS开发用windows系统的不多，其实不要纠结：其实跨平台http抓包工具charles也可以做远程代理，也就是说iOS也可以通过charles来调试，charles调试的相关技术都可以对iOS适用。  

那怎么才能实现charles做iOS的远程代理呢？先看charles官网的文档。

>USING CHARLES FROM AN IPHONE
>
>To use Charles as your HTTP proxy on your iPhone you must manually configure the HTTP Proxy settings on your WiFi network in your iPhone's Settings. Go to the Settings app, tap Wi-Fi, find the network you are connected to and then tap the blue disclosure arrow to configure the network. Scroll down to the HTTP Proxy setting, tap Manual. Enter the IP address of your computer running Charles in the Server field, and the port Charles is running on in the Port field (usually 8888). Leave Authentication set to Off. All of your web traffic from your iPhone will now be sent via Charles. You should see a prompt in Charles when you first make a connection from the iPhone, asking you to allow the traffic.Remember to disable the HTTP Proxy in your Settings when you stop using Charles, otherwise you'll get confusing network failures in your applications!

<!--more-->

![](/assets/images/remote-debug-with-charles-proxy/settings.png)

从这段文档可以知道只需要打开iOS的WiFi设置，滚屏到底部的代理设置部分，点击“手动”页签, 在“**服务器**”栏输入charles所在系统的网络IP地址，在"**端口**"栏输入“**8888**”，然后随便打开一个需要网络的APP，这个时候OSX系统上的charles会出现一个弹窗，这是一个授权远程代理的确认框，点击”**allow**”允许iOS连接本机的charles。

![](/assets/images/remote-debug-with-charles-proxy/allow.png)
   
经过这样设置后，所有iOS上的http请求都可以被charles抓到包，看到“**sequence**”视图疯狂滚屏，你也会有瞬间高潮了一样的感觉！

![](/assets/images/remote-debug-with-charles-proxy/package.png)