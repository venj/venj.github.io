---
layout: post
title: "【更新】把程序窗口放在屏幕中央"
date: 2011-04-09 12:55
comments: true
categories: NSWindow
---

在写程序的时候，我们往往需要把窗口放在屏幕中央。方法其实很简单，代码如下：  
  
``` objc
- (void)awakeFromNib {
    CGRect windowRect = [self.window frame];
    CGRect screenRect = [[NSScreen mainScreen] frame];
    CGPoint newOrigin;
    newOrigin.x = (screenRect.size.width / 2) - (windowRect.size.width / 2);
    newOrigin.y = (screenRect.size.height / 2) + (windowRect.size.height / 2);
    [self.window setFrameOrigin:newOrigin];
}
```
如果程序只有一个窗口，那么这段代码也可以放在`AppDelegate`的`-applicationDidFinishLaunching:`方法里。如果放在`-applicationDidFinishLaunching:`里，很可能会引起窗口“闪”到屏幕中央（除非程序特别小，小到几乎无需初始化），因此，放在`-awakeFromNib`里才是正确的做法。  
  
**2011-08-15更新：**  
  
事实上`NSWindow`已经有了一个专门的方法来处理窗口居中的：`- (void)center`。  
``` objc
- (void)awakeFromNib {
    [self.window center];
}
```
**不仔细看文档害死人啊！**

（全文完）