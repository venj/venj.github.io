---
layout: post
title: "在程序中动态设置是否显示Dock图标"
date: 2011-08-06 21:50
comments: true
categories: [LSUIElement, NSMenu]
---

关于如何隐藏Dock图标的方法，相信大家都知道——即使没有没有编程经验的老用户都知道。那就是，在Application Bundle里的`Info.plist`中增加一个`LSUIElement`的键，设置值为`YES`。但是，在程序中往往要让用户选择是否启用或禁用Dock图标。而程序是不能修改Application Bundle里的`Info.plist`的，因为这样会破坏程序的数字签名，所以只能在程序代码中动态的设置Dock图标的状态。

其实动态设置Dock图标的方法很简单严格的说只有两行代码：

``` objc
ProcessSerialNumber psn = { 0, kCurrentProcess };
TransformProcessType(&psn, kProcessTransformToForegroundApplication);
```

在设置隐藏Dock图标的时候需要重启应用程序，无法在运行时动态完成的，而在显示Dock图标的时候则不需要。我做了一个简单的Demo程序。放到了Github上，有兴趣的可以看看。（以后在这里出现的其他示例代码也会放到那里。）

{% img center /images/posts/dynamic-dock-icon.png %} 

程序很简单，就一个复选框，用来设置是否显示Dock图标。通过一个`-toggleDockIcon:`方法来设置是否显示Dock图标。并且在`-applicationDidFinishLaunching:`方法，设置在程序初始化的时候是否需要显示Dock图标。主要代码如下：
<!-- more --> 
``` objc
- (void)applicationDidFinishLaunching:(NSNotification *)aNotification
{
    // 读取用户设置，判断是否显示Dock图标，并且设置复选框的状态。
    defaults = [NSUserDefaults standardUserDefaults];
    BOOL iconInDock = [[NSUserDefaults standardUserDefaults] boolForKey:kShowDockIcon];
    
    if (iconInDock) {
        ProcessSerialNumber psn = { 0, kCurrentProcess };
        TransformProcessType(&psn, kProcessTransformToForegroundApplication);
        [self.dockIconSelector setState:NSOnState];
    }
    else {
        [self.dockIconSelector setState:NSOffState];
    }
    
   // 省略其他用来处理Menulet的代码。
}

- (IBAction)toggleDockIcon:(id)sender {
    NSUInteger state = [sender state];
    // 根据复选框是否选中，设置用户值，并且改变程序运行状态。
    if (state == NSOnState) {
        ProcessSerialNumber psn = { 0, kCurrentProcess };
        TransformProcessType(&psn, kProcessTransformToForegroundApplication);
        self.infoText.hidden = YES;
        
        [defaults setBool:YES forKey:kShowDockIcon];
        [defaults synchronize];
    }
    else {
        [defaults setBool:NO forKey:kShowDockIcon];
        self.infoText.hidden = NO;
        [defaults synchronize];
    }
}
```

虽然本文比较水，并且实际上只介绍了2行代码，但是这个技巧非常实用，所以推荐之。

（全文完）
