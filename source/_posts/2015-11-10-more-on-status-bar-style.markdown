---
layout: post
title: "再说说iOS的Status Bar Style"
date: 2015-11-10 23:53:38 +0800
comments: true
categories: ['Status Bar', 'UIViewController']
---

在[去年的博文](/blog/view-controller-based-status-bar-style-and-uinavigationcontroller/)中，我们曾经探讨过关于`UINavigationController`的状态栏样式（Status Bar Style）行为。当时也给出了一个较为理想的解决`UINavigationController`的Status Bar Style与子`UIViewController`不同步的问题。但是这个问题并没有结束，事实上，除了`UINavigationController`之外，还有很多场景下你需要使用文章中提及的Hack。比如，`UITabBarController`就是一个典型的例子。

<!-- more -->

#### 一. `UITabBarController`

`UITabBarController`并不会像`UINavigationController`一样用`.Default`风格覆盖子ViewController的`preferedStatusBarStyle`。一般情况下`UITabBarController`是很正常的。然而在某些特殊的情况下，问题就来了。比如使用[PKHUD](https://github.com/pkluz/PKHUD)的时候。

PKHUD会在应用默认的`window`上蒙一层新的`UIWindow`，用来显示HUD提示。而这个新的window应该是根据默认window的`rootViewController`的`preferedStatusBarStyle`来作为其状态栏样式。于是，当`UITabBarController`的自ViewController的`preferedStatusBarStyle`用`.LightContent`，在PKHUD出现时，系统的状态栏会变成`.Default`样式（因为UITabbarController未特别指定状态栏样式，所以使用了默认的样式），HUD消失后再变回去。

这样的效果非常诡异。不过知道了问题的根源，解决起来也很简单。只要像`UINavigationController`一样，为`UITabBarController`设置`preferedStatusBarStyle`即可。

``` swift
extension UITabBarController {
    override public func preferredStatusBarStyle() -> UIStatusBarStyle {
        guard selectedViewController != nil else { return .Default }
        return selectedViewController!.preferredStatusBarStyle()
    }
}
```

这样，PKHUD的显示就正常了。虽然PKHUD是目前我发现的第一个例子，但是很可能其他使用`UIWindow`对象的控件在与`UITabBarController`一起使用的时候，也会有同样的问题。而上面这个代码片段就是解决方法。

#### 二. `UINavigationController`在全屏Modal中显示时

如果你要在全屏Modal中显示一个`UINavigationController`，而它的`rootViewController`来自第三方框架，你无法直接修改该ViewController的`preferedStatusBarStyle`方法。为了UINavigationController也能使用`.LightContent`，你也可以像上面一部分中，扩展第三方类。

但是这么做的缺点也很明显，如果你在同一个应用中的其它部分需要用到这个第三方ViewController，但此时你并不需要`.LightContent`，这时候就又陷入了困境。

考虑到作为Modal出现的`UINavigationController`的Status Bar Style通常与呈现它的ViewController一致，而非和其子ViewController一致，因此，我们把之前的`UINavigationController`的扩展，修改成如下：

``` swift
extension UINavigationController {
    func preferredStatusBarStyle() -> UIStatusBarStyle {
        if self.isBeingPresented() {
            // 当它被显示时，presentingViewController通常不会为nil。
            return self.presentingViewController!.preferredStatusBarStyle()
        }
        else {
            guard self.topViewController != nil else { return .Default }
            return (self.topViewController!.preferredStatusBarStyle());
        }
    }
}
```

这样，就能避免为各种ViewController写一样的扩展了。至此，关于状态栏样式的新问题也已经解决。如您有更好的方案，或者对状态栏样式有深入的理解，希望您不吝留言赐教。谢谢。

（全文完）