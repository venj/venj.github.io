---
layout: post
title: "UINavigationController和View Controller-based状态栏风格"
date: 2014-07-24 10:30:22 +0800
comments: true
categories: [iOS7, UINavigationController, "Status Bar Style"]
---

从iOS 7开始， `UIViewController`有了一个新的方法：`- preferredStatusBarStyle`，可以让用户指定状态栏风格。但问题是这个方法只有在`ViewController`不包含在`UINavigationController`中时才起作用。大部分情况下，`ViewController`不会单独使用，一般都会嵌套在`UINavigationController`中的。因为不知道这一点，所以在很长一段时间内，我都很困惑，明明我已经在我的`ViewController`里写了`- preferredStatusBarStyle`, 却一点都不起作用。

知道了原因，接下来就简单了。我们可以写一个`UINavigationController`的扩展，覆盖其默认实现，返回最上面的`ViewController`的`preferredStatusBarStyle`。

代码如下：
<!-- more -->

``` objc
//UINavigationController+StatusBar.h
#import <UIKit/UIKit.h>

@interface UINavigationController (StatusBar)
- (UIStatusBarStyle)preferredStatusBarStyle;
@end

//UINavigationController+StatusBar.m
#import "UINavigationController+StatusBar.h"

@implementation UINavigationController (StatusBar)
- (UIStatusBarStyle)preferredStatusBarStyle {
    return [[self topViewController] preferredStatusBarStyle];
}
@end
```

用Swift来写：

```
extension UINavigationController {
    override public func preferredStatusBarStyle() -> UIStatusBarStyle {
        return self.topViewController.preferredStatusBarStyle()
    }
}
```

然后，在需要使用`UINavigationController`的时候，引入`UINavigationController+StatusBar.h`头文件就可以了。如果你用Swift，增加了`extension`就完成了。

补充：

要使用View Controller Based Status Bar Style，你可能需要在项目的的Info.plist里增加一条记录：“View controller-based status bar appearance”，并将其值设置成`YES`。

参考来源：[Being Objective…](http://mythodeia.wordpress.com/2014/05/09/view-controller-based-status-bar-appearance/)

示例代码(Swift)：[NavStatusStyle](https://github.com/venj/Cocoa-blog-code/tree/master/NavStatusStyle)

（全文完）