---
layout: post
title: "在Cocoa程序中进行屏幕截图"
date: 2011-09-01 21:10
comments: true
categories: NSTask
---

用过QQ for Mac的都知道QQ能够直接截图发送。那么，我们如何在Cocoa程序中嵌入截图支持呢？有一个最简单的方法是使用OSX自带的`screencapture`命令行工具来进行截图。

{% img center /images/posts/cocoa-screenshot-1.png %}

下面用代码来进行说明：
<!-- more --> 
``` objc
- (IBAction)grabScreen:(id)sender {
    NSTask *capture = [[[NSTask alloc] init] autorelease];
    capture.launchPath = @"/usr/sbin/screencapture";
    // -i参数表示交互模式，-c参数表示把截图保存到剪贴板
    capture.arguments = [NSArray arrayWithObjects:@"-i", @"-c", nil];
    
    [capture setTerminationHandler: ^(NSTask *t) {
        NSPasteboard *pboard = [NSPasteboard generalPasteboard];
        NSPasteboardItem *pboardItem = [[pboard pasteboardItems] objectAtIndex:0];
        NSString *pboardItemType = [[pboard types] objectAtIndex:0];
        NSData *imageData = [pboardItem dataForType:pboardItemType];
        NSImage *image = [[NSImage alloc] initWithData:imageData];
        [self.imageView setImage:image];
        [image release];
    }];
    
    [capture launch];
}
```

原理很简单，就是通过`NSTask`运行`screencapture`截图，并把截到的图保存在系统剪贴板。然后用`NSTask`的`terminationHandler:`来处理图片，这个例子中是显示到一个`imageView`中。

你可以创建一个实验项目测试一下上面的代码，示例代码已经[推送到Github上了](https://github.com/venj/Cocoa-blog-code/tree/master/Screen%20Grab)。下面是运行效果：

{% img center /images/posts/cocoa-screenshot-2.png %}

在Cocoa中屏幕截图还有很多方法，比如，苹果的开发者文档里就提供了一种用`CGWindow`截图的示例代码。这种方法无需调用外部程序，如果有需要，你可以参考一下。

（全文完）
