---
layout: post
title: "把程序移动到/Applications文件夹"
date: 2011-09-10 15:46
comments: true
categories: [LetsMove]
---

OSX有一个非常好的文件系统结构，默认的文件系统职能明确。一个很典型的例子是`Applications`文件夹，专门用来存放Mac应用程序。不过有时用户在下载了你的应用程序之后并没有养成把程序放到`/Applications`的习惯，你可以选择不去干预，也可以通过一个友好的提示来帮助用户把程序移动到`/Application`文件夹中。

要在程序中实现这样的功能其实很简单，因为已经有人帮我们实现了这样的一个方法：[LetsMove](https://github.com/potionfactory/LetsMove)。使用方法也很简单，在`ApplicationDelegate`中的`-applicationDidFinishLaunching:`方法的最前面增加一行代码，如下：
<!-- more --> 
``` objc
- (void)applicationDidFinishLaunching:(NSNotification *)aNotification
{
    PFMoveToApplicationsFolderIfNecessary(); // 增加这行代码。
    
    // Other Code…
}
```

这样就能够在程序开始的时候提示用户是否要移动应用程序了。如下所示：

{% img center /images/posts/letsmove.png %}

有一点需要注意的是，需要在编译项目的时候添加**Security.framework**支持。我做了一个示例项目，已经[推送到github上了](https://github.com/venj/Cocoa-blog-code/tree/master/Move%20It)，你也可以参考LetsMove的源码中的示例项目来了解使用方法。在OSX Lion中编译的时候可能会提示方法Depreciated，不过程序能够正常运行，相信LetMove项目的开发人员会在以后更新代码，解决方法Depreciated的问题。

<del>注：目前LetsMove不包含中文的本地语言，你可以自己做一个，或者参考我Fork的LetsMove里的中文翻译。</del>

【更新】LetsMove的作者已经合并了我的Pull请求，现在LetsMove有简体中文翻译了。

（全文完）
