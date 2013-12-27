---
layout: post
title: "跳动Dock图标的实现"
date: 2012-03-09 09:13
comments: true
categories: [NSApp, Dock]
---

OS X程序，在执行后台任务的时候，有时需要通过跳动Dock图标来提示用户进行操作或某项操作已经执行完成。方法很简单，`NSApplication`有一个`-requestUserAttention:`的方法可以跳动Dock图标。

下面是实例代码：

``` objc 
//跳动一次
- (void)bounceOnce:(id)sender {
    [NSApp requestUserAttention:NSInformationalRequest];
}
//总是跳动，直到用户干预为止
- (void)bounceAlways:(id)sender {
    [NSApp requestUserAttention:NSCriticalRequest];
}
```

FYI: `NSApp`是一个指向当前运行的程序实例的全局变量，等同于调用：`[NSApplication sharedApplication]`。

示例代码已经上传到[github](https://github.com/venj/Cocoa-blog-code/tree/master/Bounce%20Icon)，有兴趣的可以看看。不过记得在测试示例程序的时候，点击按钮后就让程序进入后台运行。5秒钟后就能看到效果了。