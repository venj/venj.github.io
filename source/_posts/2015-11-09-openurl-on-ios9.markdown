---
layout: post
title: "iOS 9下使用openURL:"
date: 2015-11-09 16:12:18 +0800
comments: true
categories: ['iOS 9', 'openURL']
---

iOS 9在各方面增强了安全性，最明显的变化，除了[ATS](/blog/app-transportation-security/)之外，那就是openURL的变化了。在iOS 9之前，应用可以使用`UIApplication`的`openURL:`方法，打开任意自定义Scheme的链接时（比如twitter://， fb://等）－－只要iOS支持。从iOS 9开始，相信很多人都见过这样的对话框：

{% img center /images/posts/open-url-ios-9.png %}

<!-- more -->

所有使用iOS 9以前的SDK作为Base SDK编译的应用，在使用`openURL:`打开其他应用时默认就会弹出这样的确认对话框，以防止未经用户授权的随意的应用转跳。而用iOS 9 SDK作为Base SDK编译的应用，调用`openURL:`就会出错。在Xcode的Console会出现类似如下的错误消息：

```
This app is not allowed to query for scheme xxx
```

要让你的App能打开某个自定义Scheme的链接，你需要在Info.plist中声明你的应用需要让`openURL:`支持的Scheme类型，如下：

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
	<string>twitter</string>
	<string>fb</string>
</array>
```

这样，你就可以调用`openURL:`来打开自定义Scheme的链接了。

参考：

1. [Apple Dev Forum](https://forums.developer.apple.com/thread/3781)
2. [Use Your Loaf](http://useyourloaf.com/blog/querying-url-schemes-with-canopenurl.html)
3. [Awkward Hare](http://awkwardhare.com/post/121196006730/quick-take-on-ios-9-url-scheme-changes)

（全文完）
