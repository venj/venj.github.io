---
layout: post
title: "在iOS程序中调用Mobile Safari打开网址"
date: 2011-05-15 00:11
comments: true
categories: [NSWorkspace, UIApplication]
---

在Cocoa中，我们可以用`NSWorkspace`的`-openURL:`来来打开系统的默认浏览器。

``` objc
- (IBAction)clickLink:(id)sender {
    [[NSWorkspace sharedWorkspace] openURL:[NSURL URLWithString:@"http://google.com"]];
}
```

不过Cocoa Touch没有这个`NSWorkspace`。那么应该怎么做呢？事实上`UIApplication`提供了同样的方法：

``` objc
- (IBAction)clickLink:(id)sender {
    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"http://google.com"]];
}
```

很简单吧！`NSWorkspace`和`UIApplication`真是异曲同工啊。事实上，因为iOS独占式的程序运行方式，用`UIApplication`来取代`NSWorkspace`来执行这个任务其实是很贴切的。不是吗？

（全文完）