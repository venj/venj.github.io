---
layout: post
title: "点击按钮复制粘贴到文本框"
date: 2012-02-13 14:47
comments: true
categories: [NSText, NSTextField, First Responder]
---

相信很多读者看到标题肯定会疑惑，文本框本来就支持复制粘贴，为什么要专门写一篇日志来介绍文本框的复制粘贴呢？其实，本文要讲述的是，如何通过按下按钮，来复制粘贴文本到文本框 -- 不是通过菜单操作，也不是通过快捷键cmd-c和cmd-v来完成。

提到复制粘贴的菜单操作和快捷键操作，看过“Cocoa Programming for Mac OS X”的读者肯定知道，他们属于“nil-targeted action”，也就是说，他们的receive是在运行时被指定的。在Interface Builder中，`-copy:`，`-paste:`方法都是连接到一个占位对象“First Responder”上的。而First Responder到底是谁是在程序运行过程中动态变化着的。

事实上，在`NSTextField`这个例子中，`-copy:`和`-paste:`的Receive是一个`NSText`对象。知道了这一点，剩下的问题就是如何获取这个对象。
<!-- more -->
答案就是，在`NSWindow`中，有一个方法能够返回当前处于First Responder的`NSText`对象: `-fieldEditor:forObject:`。

下面是实现代码：

``` objc
- (IBAction)copyText:(id)sender {
    [self.sourceField becomeFirstResponder];  //让Source文本框变成First Responder
    NSText *sourceText = [self.window fieldEditor:YES forObject:self.sourceField];  //返回sourceField的NSText对象
    [sourceText selectAll:nil];  // 全选
    [sourceText copy:nil];  // 复制
}

- (IBAction)pasteText:(id)sender {
    [self.targetField becomeFirstResponder];  //让Target文本框变成First Responder
    NSText *targetText = [self.window fieldEditor:YES forObject:self.targetField];  //返回targetField的NSText对象
    self.targetField.stringValue = @"";  //清空原来文本框中的值
    [targetText paste:nil];  //粘贴
}
```

下面是我做的一个示例程序的运行截图：

{% img center /images/posts/copy-paste-with-button.png %}

怎么样，实现很简单吧。很惭愧的是，这个问题我研究了很久，也没有找到合适的关键词搜索到比较好的参考资料。最后还是翻阅文档才找到解决方案的。RTFM，果然是永恒的真理啊！

本文的示例程序的源代码我已经推送到[github](https://github.com/venj/Cocoa-blog-code/tree/master/Paste%20Button)上了，有兴趣的可以pull下来看看。

(全文完)