---
layout: post
title: "记忆窗口位置"
date: 2011-06-12 12:26
comments: true
categories: NSWindow
---

在程序中记忆窗口的位置，以便在下次打开的时候恢复记忆的位置。这对很多程序来说还是比较有必要的。方法很简单：

``` objc
- (void)windowDidMove:(NSNotification *)notification {
    // 自动记忆和恢复位置
    [self.window setFrameAutosaveName:@"Position"];
    /* 如果是基于文档的程序
    [[self.window windowController] setShouldCascadeWindows:NO];
    [self.window saveFrameUsingName:[window representedFilename]];
    */
    // 手动记忆位置
    [self.window setFrameUsingName:@"Some name"];
}
```

// 如果是手动保存，则需要手动恢复

``` objc
- (void)awakeFromNib {
    [self.window setFrameAutosaveName:@"Some name"];
}
```

// 清除记忆的位置

``` objc
- (IBAction)clearWindowPosition:(id)sender {
    [NSWindow removeFrameUsingName:@"Some name"];
}
```

很简单吧。更多可以参考[NSWindows Class Reference](http://developer.apple.com/library/mac/#documentation/Cocoa/Reference/ApplicationKit/Classes/NSWindow_Class/Reference/Reference.html)和[Window Programming Guide](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/WinPanel/WinPanel.html)文档。

（全文完）