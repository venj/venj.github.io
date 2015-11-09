---
layout: post
title: "iOS 9中的App Transport Security"
date: 2015-11-09 15:49:38 +0800
comments: true
categories: ['iOS 9', 'ATS']
---

从iOS 9开始，系统默认已经不允许iOS应用访问不安全的链接（包括HTTP，以及被认为不安全的HTTPS请求）。很多开发者在把iOS应用迁移到iOS 9时，碰到的第一个问题，大致就是这个问题了。

虽然跳过iOS 9的这个默认安全设定并不是一个好的实践，但有时候免不了需要访问一些不安全的地址。要一劳永逸的全局跳过ATS，可以直接在Info.plist中加入以下值：

<!-- more -->

```xml
<key>NSAppTransportSecurity</key>
<dict>
	<key>NSAllowsArbitraryLoads</key>
	<true/>
</dict>
```

如果你只需为部分域名跳过ATS检查，可以用类似如下的方式设置ATS。下面这个例子用于为example.com创建一个绕过ATP检查的特例，以允许iOS应用访问不安全的example.com连接。这里不对所有的Key做出一一解释，[Apple文档](https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW33)中对所有Key有详细说明。

```xml
<key>NSAppTransportSecurity</key>
<dict>
	<key>NSExceptionDomains</key>
	<dict>
		<key>example.com</key>
		<dict>
			<key>NSExceptionAllowsInsecureHTTPLoads</key>
			<true/>
			<key>NSExceptionRequiresForwardSecrecy</key>
			<false/>
			<key>NSIncludesSubdomains</key>
			<true/>
		</dict>
	</dict>
</dict>
```

（全文完）
