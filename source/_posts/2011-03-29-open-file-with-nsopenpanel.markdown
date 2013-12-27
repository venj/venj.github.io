---
layout: post
title: "用NSOpenPanel打开文件"
date: 2011-03-29 14：24
comments: true
categories: [Block, Grand Central Dispatch, Panel]
---

`NSOpenPanel`和`NSSavePanel`从Mac OS X 10.6开始把很多方法都标记为了Deprecated，文档里也很详尽的给出了替代方法。  
  
比如，要打开一个选文件的ModalView，以前是用`NSOpenPanel`的`– beginSheetForDirectory:file:types:modalForWindow:modalDelegate:didEndSelector:contextInfo:`方法。现在，必须用新的方法替代。示例代码如下：  

``` objc
-(IBAction)showOpenPanel:(id)sender {
    NSOpenPanel *panel = [NSOpenPanel openPanel];
    [panel setDirectory:NSHomeDirectory()]; // Set panel's default directory.
    [panel setAllowedFileTypes:[NSImage imageFileTypes]]; // Set what kind of file to select.
    // More panel configure code.
    [panel beginSheetModalForWindow:[myView window] completionHandler: (^(NSInteger result){
        if(result == NSOKButton) {
            NSArray *fileURLs = [panel URLs];
            NSImage *image = [[NSImage alloc] initWithContentsOfURL:
                              [fileURLs objectAtIndex:0]];
            NSLog(@"%@", [fileURLs objectAtIndex:0]);
            // Deal with the image.
            [image release];
        }
    })];
}
```

用这个新方法利用了Objective-C 2.0的新特征：Block。在引入了Block之后，这个方法也就变成了多线程的了——这是Grand Central Dispatch的使用实例之一。  
  
这段代码的调试输出如下（多线程）：  
  
```
2011-03-29 13:49:54.204 CustomView[50396:903] In app delegate: 1.000000
[Switching to process 50396 thread 0xad07]
[Switching to process 50396 thread 0x903]
[Switching to process 50396 thread 0x9633]
[Switching to process 50396 thread 0x903]
2011-03-29 13:50:04.528 CustomView[50396:903] x-marsedit-filelocal://localhost/Users/venj/Pictures/Old%20Photo/IMG_2443.JPG
Program ended with exit code: 0
```
  
详情参考ADC文档：[在这里](http://developer.apple.com/library/mac/#documentation/cocoa/reference/ApplicationKit/Classes/NSOpenPanel_Class/DeprecationAppendix/AppendixADeprecatedAPI.html%23//apple_ref/occ/instm/NSOpenPanel/beginSheetForDirectory:file:types:modalForWindow:modalDelegate:didEndSelector:contextInfo:)。  
  
**Updated：**另外，值得注意的是，`NSOpenPanel`是`NSSavePanel`的子类。  

（全文完）