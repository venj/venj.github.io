---
layout: post
title: "在Sandbox的程序中加入Growl支持"
date: 2012-03-07 16:00
comments: true
categories: [App Sandbox, Growl]
---

Growl是一个知名的第三方消息提示框架，几乎是Mac装机必备的软件。虽然从Growl 1.3开始，Growl变成了收费软件，但是它依然是Mac App Store收费榜名列前茅的软件。虽然从OS X 10.8 Mountain Lion开始，OS X将加入Notification Center的支持，但是现在距离OS X 10.8发布依然有一段时间，而且，OS X 10.8之前的Mac OS X要退出历史舞台还有很长的路。

我曾经撰写过[关于Growl的文章](http://cocoa.venj.me/blog/add-growl-support-in-your-apps-add-growl-framework/)，因此关于Growl.framework的基本使用的内容我在本文中不再赘述了，大家可以去[回顾一下](http://cocoa.venj.me/blog/add-growl-support-in-your-apps-coding-growl/)。基本上就是：

* [下载](http://growl.info/downloads)[Growl SDK](http://growl.cachefly.net/Growl-1.3.1-SDK.zip)
* 解压，添加Framework目录下的Growl.framework到项目中；
* 添加一个plist文件，名为：Growl Registration Ticket.growlRegDict，添加合适的值（不详述）；
* 增加一个Copy Build Phase，设置为Framework，把Growl.framework加入进去；
* 在代码中实现`GrowlApplicationBridgeDelegate`的方法；
* 发布Growl Notification。

<!-- more -->

Sandbox的程序不能直接向Growl的Helper程序发送消息，支持Sandbox的程序需要使用XPC（进程间通讯）的方式与Growl交互。因此，在完成和以前一样的步骤之后，我们还有一些额外的工作要做。

我们需要往项目中加入一个Run Script的Build Phase。这个Build Phase的作用是执行一个Ruby脚本，把SDK里，`XPC Client`目录下的`com.company.application.GNTPClientService.xpc`这个包修改成适合当前项目的包。脚本和`.xpc`包在Growl SDK里都能找到。

假设，我们把SDK里的`XPC Client`文件夹复制到项目目录中，在Build Phase中添加下列shell脚本：

``` bash
XPC_START="$BUILT_PRODUCTS_DIR"
XPC_SCRIPT="$SRCROOT/XPC Client/xpc-rename-move.rb"
ruby "$XPC_SCRIPT" "$SRCROOT/XPC Client/" "$BUILT_PRODUCTS_DIR/$WRAPPER_NAME" "$CODE_SIGN_IDENTITY"
```

{% img center /images/posts/growl-script-phase.png %}

上面的代码中两个变量需要注意。`XPC_SCRIPT`是指定脚本的路径，如果你没有像我一样把XPC Client文件夹复制到到项目目录中，你可以用绝对路径。另外一个就是脚本第三行的`"$SRCROOT/XPC Client/"`。这个参数指定的是`.xpc`文件所在的文件夹，和`XPC_SCRIPT`这个变量一样，你也可以使用绝对路径。

要让Growl支持Sandbox的程序，你还要实现一个delegate方法，告诉Growl.framework程序是否有网络访问的Entitlement。我的示例程序中如下：

``` objc
- (BOOL) hasNetworkClientEntitlement {
    return NO;
}
```

完成之后，你就可以启用Entitlement，指定Code Sign的数字签名，开始编译了。

老规矩，我还是做了一个简单的示例程序，下面是执行效果：

{% img center /images/posts/growl-sandbox-app.png %}

程序已经能够正常的通过XPC发送Growl消息，并且已经是Sandbox的程序了。

我已经把代码推送到[github](https://github.com/venj/Cocoa-blog-code/tree/master/Growl%20Test)上去了，项目名称是"Growl Test"（注意区别之前的"GrowlTest"项目），如果你遇到什么问题，可以Checkout出来看看我的设置。注意，测试Sandbox是需要付费开发者帐号的。（不是付费开发者帐号也没必要做Sandbox的程序，不是么？ :P ）

(全文完)