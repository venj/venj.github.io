---
layout: post
title: "为应用程序增加Growl支持（下）：在代码中应用Growl"
date: 2011-05-26 21:07
comments: true
categories: [Growl, Framework]
---

在[之前一篇日志](/blog/add-growl-support-in-your-apps-add-growl-framework/)里，我简单的介绍了一下如何在项目中增加Growl.framework。现在，我来介绍一下如何使用Growl。在之前的日志里，我演示的时候，是往项目中加入Growl.framework，这次，为了完整起见，我将使用Growl-WithInstaller.framework来演示。

这两个框架的区别在于后者把Growl安装程序打包进了框架中。如果用户没有安装Growl，并且愿意安装，可以马上安装。但是很多人并不喜欢这种捆绑安装的方式，很多人把责任归咎于Growl，Growl甚至专门为此[写了个网页](http://growl.info/thirdpartyinstallations.php)解释。其实Growl是出于好意，并且，选择哪个框架是开发人员的事情。作为用户，如果你真的不喜欢Growl，Growl能够很方便的删除；而作为开发人员，应该根据目标用户谨慎选择捆绑Growl安装程序的包。

好了，题外话不多说，言归正传。
<!-- more --> 
我们首先在Xcode里新建一个简单的Cocoa项目，按照前文的方法，加入Growl支持。

要让Cocoa程序支持Growl，首先要为Growl设置一个`Delegate`。简单起见，我们在`AppDelegate`的`-(void)applicationDidFinishLaunching:(NSNotification *)aNotification`中，设置`Delegate`方法：

``` objc
[GrowlApplicationBridge setGrowlDelegate:self];
```

在显示提示信息之前，我们首先要“注册”提示信息。注册的方法有两种，一种是在项目中加入一个名为“Growl Registration Ticket.growlRegDict”的文件，格式为property list。Growl会自动加载这个文件。

{% img center /images/posts/growl-code-1.png %}

这个plist包含三个键值对。第一个键是`TicketVersion`，值为整数类型，目前取值为`1`；第二个键是`AllNotifications`，值为一个数组，其中包含所有的`Notification`的名称，类型为字符串；第三个键是`DefaultNotifications`，值为一个数组，是`AllNotifications`的一个子集，作为默认启用的`Notification`。大多数情况下，是相同的。至于为什么要有这两种数组的区分，我也没弄得很明白。

另一种注册提示信息的方法是实现一个`delegate`方法：`-(NSDictionary *)registrationDictionaryForGrowl`。这个方法返回的辞典必须包含两个键：`GROWL_NOTIFICATIONS_ALL`和`GROWL_NOTIFICATIONS_DEFAULT`。作用自然不用多说，跟上述的`AllNotifications`和`DefaultNotifications`一一对应。

``` objc
- (NSDictionary *)registrationDictionaryForGrowl {
    NSArray *allNotes = [NSArray arrayWithObjects:@"myNotification", @"myNote", @"notDefaultNote", nil];
    NSArray *defaultNotes = [NSArray arrayWithObjects:@"myNotification", @"myNote", nil];
    
    return [NSDictionary dictionaryWithObjectsAndKeys:allNotes, GROWL_NOTIFICATIONS_ALL, defaultNotes, GROWL_NOTIFICATIONS_DEFAULT, nil];
}
```
如果没有利用任何一种方法注册提示信息，程序在编译时，在控制台上会出现下面这样错误信息：

```
2011-05-26 20:08:13.469 GrowlTest[1644:903] GrowlApplicationBridge: The Growl delegate did not supply a registration dictionary, and the app bundle at /Users/venj/Library/Developer/Xcode/DerivedData/GrowlTest-hetkeinkpxbuynbhprgdyoywkrif/Build/Products/Debug/GrowlTest.app does not have one. Please tell this application's developer.2011-05-26 20:08:13.668 GrowlTest[1644:903] not installed
```

不过Growl提示还是能够显示的。至于注册和不注册的区别，我也没弄明白，囧。

注册完毕之后，你就能显示Growl提示了。我们假设在Cocoa项目中，增加了一个按钮，连接到下面这个Action上：

``` objc
- (IBAction)showGrowl:(id)sender {
    [GrowlApplicationBridge notifyWithTitle:@"The Title" description:@"This is a notification description." notificationName:@"myNote" iconData:nil priority:0 isSticky:NO clickContext:nil];
}
```

Growl提示的核心方法是：

``` objc
+[GrowlApplicationBridge notifyWithTitle:
    description:
    notificationName:
    iconData:
    priority:
    isSticky:
    clickContext:]
```

其中，`title`（`NSString`）, `description`（`NSString`）, `iconData`（`NSData`）, `isSticky`（`BOOL`）的值在下图中表示了一下。其中，Sticky的值如果设置为`YES`，那么必须要用户点击关闭按钮，提示才会消失；否则，提示会自动消失。

{% img center /images/posts/growl-code-2.png %}

`priority`的取值为包括-2, -1, 0, 1, 2，从左到右优先级递增。Priority通常没啥作用。如果你在Growl的控制面板中，为不同优先级设置了不统的提示背景和提示文字颜色，那么这些优先级就会反映出来。

{% img center /images/posts/growl-code-3.png %}

最后一个`context`，接收一个`id`类型的值。这个值与`-(void)growlNotificationWasClicked:(id)clickContext`这个回调方法有密切联系。如果`context`为`nil`，这个方法将不会调用；如果`context`不是`nil`，那么`context`的值就会作为`clickContext`的值，传入这个回调方法。在Growl提示被点击时候，会触发这个回调方法，执行相应的动作。

另外，还有几个`delegate`方法，我来简单介绍一下：

`-(void)growlIsReady:`这个方法主要是在程序使用了Growl-WithInstaller.framework时，用户选择安装或升级Growl，并成功启动之后，所执行的动作。

`-(void)growlNotificationTimedOut:(id)clickContext:`与`-(void)growlNotificationWasClicked:(id)clickContext`类似，这个方法是在用户没有对提示作出响应，在Growl即将消失的时候调用的一个方法。

{% img center /images/posts/growl-code-4.png %}

`-(NSData *)applicationIconDataForGrowl`和`-(NSString *)applicationNameForGrowl`，设置Growl返回的程序名称和程序图标。通常如果不设置，Growl会使用默认的程序图标和`Info.plist`里指定的程序名称。比如下面的两个图中，我们并没有为示例程序设置程序图标，而默认的程序名称为“GrowlAppTest”。我们在实现了这两个方法之后，可以覆盖默认行为，这样，在Growl设置和Growl提示里，我们就能看到图标和程序名称了。

{% img center /images/posts/growl-code-5.png %}

{% img center /images/posts/growl-code-6.png %}

另外，假如用户没有安装Growl，那么应用Grow-WitiInstaller.framework的时候，程序在第一次调用Growl方法的时候会提示用户安装Growl。

{% img center /images/posts/growl-code-7.png %}

关于如何自定义这个对话框“说服”用户安装Growl，还有几个`Delegate`方法来做到。我就不一一叙述了：

``` objc
- (NSString *)growlInstallationWindowTitle;
- (NSString *)growlUpdateWindowTitle;
- (NSAttributedString *)growlInstallationInformation;
- (NSAttributedString *)growlUpdateInformation;
```

最后，我就连篇累牍的奉上示例程序的源代码：

``` objc
/* GrowlTestAppDelegate.h */
#import <Cocoa/Cocoa.h>
#import "Growl-WithInstaller/Growl.h"

@interface GrowlTestAppDelegate : NSObject <NSApplicationDelegate, GrowlApplicationBridgeDelegate> {
@private
    NSWindow *window;
}

@property (assign) IBOutlet NSWindow *window;
- (IBAction)showGrowl:(id)sender;

@end

/* GrowlTestAppDelegate.m */

#import "GrowlTestAppDelegate.h"

@implementation GrowlTestAppDelegate

@synthesize window;

- (void)applicationDidFinishLaunching:(NSNotification *)aNotification
{
    // Insert code here to initialize your application
    [GrowlApplicationBridge setGrowlDelegate:self];
    if (![GrowlApplicationBridge isGrowlInstalled]) {
        NSLog(@"not installed");
    }
}

- (IBAction)showGrowl:(id)sender {
    [GrowlApplicationBridge notifyWithTitle:@"The Title" description:@"This is a notification description." notificationName:@"myNote" iconData:nil priority:0 isSticky:NO clickContext:@"God"];
}

# pragma mark - Growl Delegate

//- (NSDictionary *)registrationDictionaryForGrowl {
//    NSArray *allNotes = [NSArray arrayWithObjects:@"myNotification", @"myNote", @"notDefaultNote", nil];
//    NSArray *defaultNotes = [NSArray arrayWithObjects:@"myNotification", @"myNote", nil];
//
//    return [NSDictionary dictionaryWithObjectsAndKeys:allNotes, GROWL_NOTIFICATIONS_ALL, defaultNotes, GROWL_NOTIFICATIONS_DEFAULT, nil];
//}

- (void) growlIsReady {
    NSLog(@"Growl is up and running.");
    [self showGrowl:nil];
}

- (void) growlNotificationTimedOut:(id)clickContext {
    NSLog(@"%@, I'm disappearing...", clickContext);
}

- (void) growlNotificationWasClicked:(id)clickContext {
    NSLog(@"%@, I'm clicked...", clickContext);
}

- (NSData *) applicationIconDataForGrowl {
    NSData *data = [NSData dataWithContentsOfFile:[[NSBundle mainBundle] pathForImageResource:@"lame"]];
    return data;
}

- (NSString *) applicationNameForGrowl {
    return @"GrowlAppTest";
}

# pragma mark - Growl Installer Delegate

- (NSString *)growlInstallationWindowTitle {
    return @"Install Growl";
}

- (NSString *)growlUpdateWindowTitle {
    return @"Update Growl";
}

//- (NSAttributedString *)growlInstallationInformation {
//    // Some inplementation
//}
//
//- (NSAttributedString *)growlUpdateInformation {
//    // Some inplementation
//}

@end
```

（全文完）
