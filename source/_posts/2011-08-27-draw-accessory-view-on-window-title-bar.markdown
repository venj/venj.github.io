---
layout: post
title: "在NSWindow窗口标题栏上绘制辅助视图"
date: 2011-08-27 1:25
comments: true
categories: [NSWindow]
---

如果你用过[Acorn](http://flyingmeat.com/acorn/)的试用版，你一定知道Acorn的试用版的文档窗口的标题栏上有一个提示过期的按钮。虽然标题栏不能被滥用来绘制控件，但是有时候，恰到好处的使用会改善用户体验。比如说，Lion的全屏按钮就是放在标题栏上的；[Chocolat](http://chocolatapp.com/)文本编辑器也放了一个新建标签的按钮在标题栏上，Lion全屏按钮的旁边。
<!-- more --> 
{% img center /images/posts/window-titlebar-1.png Acorn的试用提示 %}

{% img center /images/posts/window-titlebar-2.png Lion窗口的全屏按钮 %}

{% img center /images/posts/window-titlebar-3.png Chocolat的新建标签按钮%}

初看起来，在标题栏上加一个辅助视图似乎很有难度；不过事实上，早就已经有人[把方法贴在Blog里](http://iloveco.de/adding-a-titlebar-accessory-view-to-a-window/)了（注意，原文里的`UIController`不是`UIKit`的类，该文发布的时候`UIKit`和iPhone还没有发布呢，因此这算是个NameSpace冲突吧，其实文中的`UIController`就是个普通的Cocoa Controller类而已）。本文算是半转载地复制代码过来，供大家参考：

``` objc
- (void)awakeFromNib {
    [self composeInterface];
}

- (void)composeInterface {
    NSView *themeFrame = [[self.window contentView] superview];
    
    NSRect themeFrameRect = [themeFrame frame];
    NSRect accessoryViewFrame = [self.accessoryView frame];
    NSRect newFrame = NSMakeRect(themeFrameRect.size.width - accessoryViewFrame.size.width,
                                 themeFrameRect.size.height - accessoryViewFrame.size.height,
                                 accessoryViewFrame.size.width,
                                 accessoryViewFrame.size.height);
    [self.accessoryView setFrame:newFrame];
    
    [themeFrame addSubview:self.accessoryView];
}
```

我做了一个示例项目，放到了本blog的[github](https://github.com/venj/Cocoa-blog-code/tree/master/TitleBar%20Addons)里。下面简述一下过程：在`WindowController`（为了示例的简便起见，我直接放在`AppDelegate`里了）里新建上述`-composeInterface:`方法，然后在`-awakeFromNib:`里调用这个方法。这个方法的原理非常简单，就是获取`window`的`contentView`的上一级View，然后把`accessoryView`定位到该View的右上角。这样我们就能够在`accessoryView`里绘制控件了。不过需要注意的是，窗口标题栏高度有限，因此`accessoryView`的高度不能超过22pt，并且，也不要太宽。不过放一个小按钮还是绰绰有余了。另外，如果有Lion的全屏按钮支持的话，需要在`accessoryView`的右侧留出全屏按钮的空间；或者在`composeInterface`方法中，设置`accessoryView`的`Frame`的时候，留出全屏按钮的空间。下面就是这个我做的示例项目的运行效果：

{% img center /images/posts/window-titlebar-4.png %}

看着是不是还是挺有范儿的？ :D 如果你有什么不清楚的地方，可以看一下示例程序的源码，对于新建项目，添加控件，设置视图等这些常规的操作我就不赘述了。

（全文完）
