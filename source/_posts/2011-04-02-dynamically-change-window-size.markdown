---
layout: post
title: "动态改变窗口大小"
date: 2011-04-02 16:28
comments: true
categories: NSView
---

根据SubView的大小动态的改变窗口大小的操作在Cocoa编程里很常用。记录方法如下。下面的代码假定通过一个Box容器，分别容纳不同大小的SubView，在切换SubView的时候，窗口的大小适应容器大小的变化。SubView的容器可以是任何`NSView`。这个代码通常放在`Controller`里。  
  
``` objc
// 通过某个View获得需要改变大小的窗口
NSWindow *window = [self.box window];
// 获取新View
NSView *view = [viewController view];
// 当前View的大小
NSSize currentSize = [[self.box contentView] frame].size;
// 新View的大小
NSSize newSize = [view frame].size;
// 新旧View的宽度和高度差
CGFloat deltaWidth = newSize.width - currentSize.width;
CGFloat deltaHeight = newSize.height - currentSize.height;
// 获取窗口大小
NSRect windowFrame = [window frame];
// 加上SubView高度和宽度的变化值
windowFrame.size.height += deltaHeight;
windowFrame.size.width += deltaWidth;
// 为了保持窗口左上角位置不变，需要重设窗口框的绘制起点的y值
// 之所以用 -= 是因为Cocoa的绘图起点是左下角
// 起点的x值无需改变。
windowFrame.origin.y -= deltaHeight;
// 移除旧View
[self.box setContentView:nil];
// 改变窗口大小，使用动画
[window setFrame:windowFrame display:YES animate:YES];
// 加入新View
[self.box setContentView:view];
```

（全文完）