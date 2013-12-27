---
layout: post
title: "Twitter for Mac风格的Sheet定位"
date: 2012-02-15 17:29
comments: true
categories: [NSTableView, Sheet, Twitter]
---

昨天在用Twitter的时候，突然意识到，在删除Twitter消息的时候弹出的Sheet是定位在要删除的那条消息的顶部，而不是窗口标题栏的下方。

{% img center /images/posts/twitter-sheet.png %}

我很好奇这是如何做到的。今天抽了点时间，在简单的查阅[文档](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/Sheets/Tasks/PositioningSheets.html#//apple_ref/doc/uid/20002082-BAJDAJHF)之后，我找到了答案。其核心就是`NSWindowDelegate`的一个方法：`-window:willPositionSheet:usingRect:`，可以指定Sheet的显示位置。

我做了一个简单的View-based Table View（Lion下），然后用下面的方法做了实现：
<!-- more -->
``` objc
// 创建一个NSAlert，并以Sheet的形式呈现
- (IBAction)removeRow:(id)sender {
    NSAlert *alert = [NSAlert alertWithMessageText:@"Are you sure?" defaultButton:@"Yes" alternateButton:@"No" otherButton:nil informativeTextWithFormat:@"Deleting a table cell is not reversable."];
    [alert beginSheetModalForWindow:self.window modalDelegate:self didEndSelector:@selector(alertDidEnd:returnCode:contextInfo:) contextInfo:NULL];
}

// NSWindowDelegate方法，用来指定Sheet的显示位置
- (NSRect)window:(NSWindow *)window willPositionSheet:(NSWindow *)sheet usingRect:(NSRect)rect {
    id cell = [self.tableView rowViewAtRow:[self.tableView selectedRow] makeIfNecessary:YES];
    NSRect cellRect = [cell frame];
    NSRect scrollViewRect = [self.tableView.superview frame];
    cellRect.origin.y = scrollViewRect.size.height - cellRect.origin.y; // View-based table view is flipped?
    cellRect.size.height = 0;
    return cellRect;
}
```

这个实现中，有一行用了并不漂亮的方法设置了`cellRect.origin.y`的值。这可能是因为Table View的坐标系是Flipped的，因此需要倒置一下，Sheet才能正确定位(注)。我做的示例程序的运行效果如下：

{% img center /images/posts/twitter-style-sheet.png %}

我已经把示例程序的源码推送到[github](https://github.com/venj/Cocoa-blog-code/tree/master/TwiTable)上了，有兴趣的读者可以clone下来研究一下。示例程序运行的时候，选中一个表格，按下cmd - delete（或者Edit - Delete菜单操作），你就能看到运行效果了。

这次碰到问题的经历再次证明了RTFM的威力所在。Happy coding and RTFM.

(注) 我不确定View-based table view是否是一个flipped的View，仅仅从实现的时候发现似乎是这样的（RTFM做的还不够好，自pia）。如果你知道细节，请留言告知，鄙人不胜感谢！

(全文完)
