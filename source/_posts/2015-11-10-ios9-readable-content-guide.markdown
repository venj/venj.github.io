---
layout: post
title: "iOS 9的TableView和Readable Content Guide"
date: 2015-11-10 14:00:02 +0800
comments: true
categories: ['UITableView', 'UIView', 'Auto Layout']
---

相信很多开发者在把应用迁移到iOS 9的时候发现了如下现象。那就是TableView在iOS 9的iPad或iPhone 6 Plus的横屏模式时，表格两边有巨大的空白边缘。

{% img center /images/posts/ios9_rcg.png %}

<!-- more -->

这是因为iOS 9为`UIView`引入了一个新的属性`readableContentGuide`，这个属性为View定义了一个可以放置用于阅读的内容的最佳区域。如果启用`readableContentGuide`的话，那么View就会把它作为边缘进行布局。但是这可能并不是我们想要的效果，要关闭TableView的Readable Content Guide，可以在`viewDidLoad:`中调用下面这段代码：

``` swift
// 在viewDidLoad:中调用：
if #available(iOS 9.0, *) {
    tableView.cellLayoutMarginsFollowReadableWidth = false
}
```

如果你用的是Interface Builder，你可以通过选中TableView，在Size Inspector中，通过勾选“Follow Readable Width”来启用Readable Content Guide（或取消选中来禁用它）。

{% img center /images/posts/ios9_rcg_ib.png %}

当然，你可以为TableView重新设置`readableContentGuide`，以对这个属性进行更加精确的控制。上面介绍的方法对于一般的应用场景，保持iOS 8以下系统的兼容性已经足够了。而`readableContentGuide`实际上是一个`UILayoutGuide`对象，它是Auto Layout中一个重要的对象，具体就不在本文中详述了。

（全文完）
