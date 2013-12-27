---
layout: post
title: "用Cocoa实现点击按钮打开快捷菜单"
date: 2011-04-16 11:41
comments: true
categories: [NSButton, NSMenu]
---

用一个自定义按钮打开菜单已经是一个很常见的需求的。不过`NSPopupButton`对于我们的这种需求来说显得有点不合适。最终的结果如下所示：  

{% img center /images/posts/image-button-menu.png 和自定义按钮关联的弹出菜单 %}

{% img center /images/posts/normal-button-menu.png 和普通按钮关联的弹出菜单 %}

实现的方法很简单，代码如下所示：  
<!-- more --> 
``` objc
- (IBAction)showMenu:(id)sender {
    
    NSRect frame = [(NSButton *)sender frame];
    NSPoint menuOrigin = [[(NSButton *)sender superview] convertPoint:NSMakePoint(frame.origin.x, frame.origin.y) toView:nil];
    
    NSEvent *event =  [NSEvent mouseEventWithType:NSLeftMouseDown
                                         location:menuOrigin
                                    modifierFlags:NSLeftMouseDownMask
                                        timestamp:0
                                     windowNumber:[[(NSButton *)sender window] windowNumber]
                                          context:[[(NSButton *)sender window] graphicsContext]
                                      eventNumber:0
                                       clickCount:1
                                         pressure:1];
    
    // contextMenu可以直接在XIB里初始化一个菜单
    [NSMenu popUpContextMenu:contextMenu withEvent:event forView:(NSButton *)sender];
}
```
啰嗦一句，关于自定义按钮的设定问题。截图中的自定义按钮就是一个普通的push button，在IB里作如下设置：  

{% img center /images/posts/button-menu-xcode.png %}

也就是取消掉Bordered，以及指定一个Image和Alternative (Image)。  

[参考文章在此](http://praveenmatanam.wordpress.com/2008/09/05/how-to-popup-context-menu-when-clicked-on-button/)

（全文完）