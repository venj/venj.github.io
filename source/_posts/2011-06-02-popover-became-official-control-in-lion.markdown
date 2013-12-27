---
layout: post
title: "Popover在Mac OS X Lion中已经成为正式控件"
date: 2011-06-02 11:38
comments: true
categories: [Lion, NSPopover, NSViewController]
---

昨天心血来潮又把Lion装上了。这次是装到了移动硬盘里。效果良好。这次只要是为了看看Lion里Cocoa的新变化，顺道学习一下——虽然我对已有的Cocoa框架还没有熟悉。

打开最新的Xcode 4.1 DP5，很高兴的发现Popover已经成为一个标准控件。Popover在Xcode 4里用的很多。因为Popover现在非常流行，所以我就忍不住要测试以下。快速搜索了一下文档，发现Popover的文档暂时还没有进入Xcode的文档系统，所以只能硬着头皮，参考头文件里的文档，做了一个测试程序，感觉良好。 ：）
<!-- more --> 
先贴个截图：

{% img center /images/posts/popover.png %}

好吧，界面上目前神马东东都没有。不过这是一个很好的状态，不是么？你可以在这个界面上自由发挥呀！

好了，下面拿代码说事儿。因为我试的时候还是走了一点点弯路的，不过既然写成文章了就不提了，直接进入正题。

用Xcode新建一个Cocoa项目。然后，创建一个`NSViewController`的子类，记得包含上`xib`（这样能省一些步骤）。我这里用的名字是`MyPopoverViewController`。代码如下：

``` objc
/* MyPopoverViewController.h */
#import <Cocoa/Cocoa.h>

@interface MyPopoverViewController : NSViewController {
    NSPopover *popOver;
}

@property (retain) NSPopover *popOver;

- (IBAction)dismissPopover:(id)sender;
@end

/* MyPopoverViewController.m */

#import "MyPopoverViewController.h"

@implementation MyPopoverViewController
@synthesize popOver;

- (id)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil
{
    self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil];
    if (self) {
        // Initialization code here.
        self.popOver = [[NSPopover alloc] init];
        self.popOver.contentViewController = self;
    }
    
    return self;
}

- (void)dealloc {
    [self.popOver release];
    
    [super dealloc];
}

- (IBAction)dismissPopover:(id)sender {
    [self.popOver performClose:sender];
}
@end
```

我为`MyPopoverViewController.xib`这样设计布局。当然，你可以随意，记得添加一个按钮，并设置`action`为`-(void)dismissPopover:(id)sender`。

{% img center /images/posts/popover-xcode.png %}

然后，在`AppDelegate`中这样调用：

``` objc
/* TestAppAppDelegate.h */
#import <Cocoa/Cocoa.h>

@class MyPopoverViewController;
@interface TestAppAppDelegate : NSObject <NSApplicationDelegate> {
    NSWindow *window;
    NSButton *myButton;
    MyPopoverViewController *popControl;
}

@property (assign) IBOutlet NSWindow *window;
@property (assign) IBOutlet NSButton *myButton;
@property (retain) MyPopoverViewController *popControl;

- (IBAction)showPopover:(id)sender;
@end

/* TestAppAppDelegate.m */
#import "TestAppAppDelegate.h"
#import "MyPopoverViewController.h"

@implementation TestAppAppDelegate

@synthesize window;
@synthesize myButton;
@synthesize popControl;

- (void)applicationDidFinishLaunching:(NSNotification *)aNotification
{
    // Insert code here to initialize your application
    self.popControl = [[MyPopoverViewController alloc] init];
}

- (IBAction)showPopover:(id)sender {
    [self.popControl.popOver showRelativeToRect:[self.myButton bounds] ofView:self.myButton preferredEdge:NSMaxXEdge];
}

- (void)dealloc {
    [self.popControl release];
    
    [super dealloc];
}

@end
```

`MainMenu.xib`也是随便设计。记得添加一个按钮，做一个Outlet，指向`myButton`，一个`action`指向`- (IBAction)showPopover:(id)sender`。

测试程序基本就是这样了。如果没啥错误的话，编译运行应该一切OK。

应该说，Popover的使用方法也是很简单的。如果你了解一些Cocoa Touch的话，你应该会发现有样东西非常熟悉——ViewController。ViewController在Cocoa中的重要性也是越来越高了，虽然目前Cocoa的`NSViewController`还远没有`UIViewController`那么重要和完整，但是应该说，这是Cocoa今后的发展方向。

好了，关于Popover就简单的写这么多吧，我也要继续学习了。。。

（全文完）