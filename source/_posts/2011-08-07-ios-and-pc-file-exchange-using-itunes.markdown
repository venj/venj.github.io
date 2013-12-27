---
layout: post
title: "让iOS App通过iTunes进行文件交换"
date: 2011-08-07 13:04
comments: true
categories: [iTunes, iOS]
---

有一些App需要通过使用iTunes让用户上传和下载文档。要让iOS程序支持iTunes文件交换其实很简单，只需要在程序的`Info.plist`里增加一个键：`UIFileSharingEnabled（Application supports iTunes file sharing）`，赋值`YES`。

{% img center /images/posts/file-exchange-xcode.png %}

这样，编译之后进行机上运行的时候，连接设备到iTunes，就能进行文件交换了。
<!-- more --> 
{% img center /images/posts/file-exchange-itunes.png %}

如果是在iOS Simulator中进行测试，可以把文件放到应用程序的“用户目录”的“Documents”下。要知道用户目录在OSX下的路径，可以`NSLog(@"%@", NSHomeDirectory());`运行一下就知道了，用户家目录在OSX下的路径类似这个：`/Users/venj/Library/Application Support/iPhone Simulator/4.3.2/Applications/158C149B-FF57-4C62-AEDB-DFB7A3BD8AFB`。我做了一个简单的程序进行测试，在Simulator中运行的时候，把文件放到用户目录下，下图是用户目录在OSX下的内容，把文件放到`Documents`下就可以了：

{% img center /images/posts/file-exchange-finder.png %}

然后在程序中测试文件有没有成功被程序识别：

``` objc
NSFileManager *manager = [NSFileManager defaultManager];
NSString *dbPath = [[NSHomeDirectory() stringByAppendingPathComponent:@"Documents"] stringByAppendingPathComponent:@"db.sqlite"];

if ([manager fileExistsAtPath:dbPath]) {
    self.navigationItem.title = @"Ready To Go";
}
else {
    self.navigationItem.title = @"No DB File";
}
```

下面是程序在添加文件前后的运行情况：

{% img center /images/posts/file-exchange-ios.png %}

我已经把示例程序放到了[Github](https://github.com/venj/Cocoa-blog-code)上，有兴趣的可以看看。更多详情可以参考iOS开发者文档。

（全文完）
