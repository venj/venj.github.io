---
layout: post
title: "为应用程序增加Growl支持（上）：在项目中加入Growl.Framework"
date: 2011-05-26 10:12
comments: true
categories: [Growl, Framework, Xcode 4]
---

Growl是啥，用来干什么的我就不介绍了。Growl的文档说实话，写的挺抽象的。不是Step by Step的文档，对我来说就有点困难了。所以，我决定把Growl支持写成几篇短文，简单介绍一下如何在程序中增加Growl支持。【为啥要分几篇？嘛，因为我自己也是边学边写文章的吗。。。Orz】

首先，介绍一下原文是怎么写的。

> 1. Download the frameworks from the Downloads page from there.
> 2. Copy the Growl framework to your application's project folder (or any subdirectory of it).
> 3. Add the Growl framework to your project, making sure that all the relevant target checkboxes are checked. The header files in the framework use UTF-8 encoding.
> 4. Add a Copy Files phase to your application's target.
> 5. Get Info on the Copy Files phase.
> 6. Set the destination to “Frameworks”, with no subpath (clear the field).
> 7. Drag the framework from the group tree into the Copy Files phase.

其实描述的听清楚的。奈何，我的理解能力太差，搞不定。俗话说，__A picture worth a thousand words__，所以我就以Xcode 4为例，把上述过程做一遍。（下面的编号基本对应上文中的1-7所述的步骤）
<!-- more --> 
1 下载SDK（[下载页](http://growl.info/index.php)）。

{% img center /images/posts/growl-1.png %}

2 挂载DMG，把Framework里的Growl.framwork或Growl-WithInstaller.framework（根据需要选择）拖拽到Xcode4的左侧项目树下。

{% img center /images/posts/growl-2.png %}

3 这时会弹出Sheet。选择“Copy items into destination group's folder(if needed)”。至于文中提到的UTF-8什么的，这个是Xcode 3.x中的东西了，现在已经没有了。

{% img center /images/posts/growl-3.png %}

4 增加一个Build Phase，操作顺序如下图。

{% img center /images/posts/growl-4.png %}

5 - 6. 在Copy Files Build Phase窗口中，将Destination设置为Framework，subpath保持默认的留空状态。

{% img center /images/posts/growl-5.png %}

7 把左侧栏里的Growl.framework拖进Copy Files Build Phase。

{% img center /images/posts/growl-6.png %}

至此，设置的部分完成了。简单测试一下：

{% img center /images/posts/growl-7.png %}

在`AppDelegate`里，加入如下代码：

``` objc
/* TestAppDelegate.h */
#import "Growl/Growl.h"  // 增加这行！

@interface TestAppAppDelegate : NSObject <NSApplicationDele, GrowlApplicationBridgeDelegate>
// 省去其它代码
@end

/* TestAppDelegate.m */
// 省去其它代码
@implementation TestAppAppDelegate
// 省去其它代码
- (void)applicationDidFinishLaunching:(NSNotification *)note {
     [GrowlApplicationBridge setGrowlDelegate:self]; // 增加这行！
// 省去其它代码
}
```

然后选择“Product - Build”，或者按下cmd-b。如果编译成功，表示你的应用程序的Growl支持已经设置成功。现在你就可以开始利用Growl了。

关于怎么在代码中利用Growl，我会在[后续文章](/blog/add-growl-support-in-your-apps-coding-growl/)中讲述。

（本文完）