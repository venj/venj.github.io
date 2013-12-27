---
layout: post
title: "自定义UINavigationBar的背景"
date: 2012-01-11 15:20
comments: true
categories: [UINavigationBar, UINavigationController]
---

为了让我们的应用程序更加美观，我们往往希望对iPhone自带的控件进行一点自定义。比如，本文即将要讲述的，给`UINavigationBar`加一个背景。

最简单的一个自定义方法就是修改一下背景色。方法非常简单，那就是使用它的`tintColor`属性：
<!-- more -->
``` objc
self.navigationController.navigationBar.tintColor = [UIColor redColor];
```

这样就轻松地为`UINavigationBar`加上了红色的背景色--当然你可以使用任何颜色。下面是模拟器中的测试效果：

{% img center /images/posts/custom-navbar-bg-color.png %}

另外，就是为`UINavigationBar`加背景图片。这个稍稍复杂一些--特别是对于iOS 5之前的iOS来说。先说说简单的，iOS 5已经为`UINavigationBar`增加了一个新的方法`-setBackgroundImage:forBarMetrics:`，专门用于设置`UINavigationBar`的背景图片。

**Updated** 

删除了在iOS4下有问题的方法。

**Update**

适用于iOS 4的方法是在`AppDelegate.m`中创建一个`UINavigationBar`的Catagory，覆盖`-drawRect:`方法，如下：

``` objc
@implementation UINavigationBar (CustomImage)
- (void)drawRect:(CGRect)rect {
    UIImage *img = [UIImage imageNamed:@"navbar"];
    [img drawInRect:rect];
}
@end

...

//在后面加入判断是否支持iOS 5的代码，来提供对iOS 5的支持：

if ([bar respondsToSelector:@selector(setBackgroundImage:forBarMetrics:)]) {
    [bar setBackgroundImage:bg forBarMetrics:UIBarMetricsDefault];
}
```

这种方法将应用到程序中所有的`UINavigationBar`实例。但是通常来说，是不推荐覆盖系统自带的类中的方法的，所以我并不推荐使用这种方法。

[这里](http://idevrecipes.com/2011/01/12/how-do-iphone-apps-instagramreederdailybooth-implement-custom-navigationbar-with-variable-width-back-buttons/)介绍了更好的方法。

(全文完)