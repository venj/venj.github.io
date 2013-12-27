---
layout: post
title: "CodeBox风格的窗口打开动画"
date: 2013-06-03 16:51
comments: true
categories: [NSWindow, Animation]
---

一直觉得[CodeBox](http://www.shpakovski.com/codebox/)的窗口呈现动画很帅气－－就是窗口Bounce了一下的那个效果。对了，就是`NSAlert`在OSX Lion下的显示的时候那个动画。我一直试图复刻那个动画，但是每次都灰头土脸的结束各种尝试。

今天搜索网络，在StackOverflow上找到了[答案](http://stackoverflow.com/questions/14591785/how-to-open-a-nswindow-with-a-popup-animation)－－事实上，我曾经多次无限接近正确答案，但最终还是没有成功。

<!-- more -->

答案实际上很简单：设置`NSWindow`对象的`animationBehavior:`为`NSWindowAnimationBehaviorAlertPanel`。

但是为啥我曾经这么设置，却一直没有成功呢？因为我一直用的非Document Based Application模版做的测试！

在Document Based Application模版下，直接在Interface Builder里，设置窗口的Animation为Alert Style，就直接达成了CodeBox的窗口呈现动画了，一行代码都不用写。但是对于非Document Based Application，你需要做一点点工作才行。

首先，在Interface Builder里设置Animation为Alert Style，然后，取消Visible At Launch前面的选择。如下图：

{% img center /images/posts/codebox_animation.png %}

然后，在`-applicationDidFinishLaunching:`方法中，加入如下代码：

```objc
dispatch_async(dispatch_get_main_queue(), ^{
    [self.window makeKeyAndOrderFront:nil];
});
```

编译，执行。Boom！窗口动画就出来了！注意了，这个动画只有OS X Lion及更新版本上可以使用。虽然我走了很多弯路，但是最终还是成功找到解决方法，一方面，显示了自己有多么无知，但是最终解决问题，也总算有一丝安慰。

(全文完)
