---
layout: post
title: "本地化加载屏幕"
date: 2013-03-11 09:00
comments: true
categories: [iOS, L10N, I18N]
---

我一直有注意到“[大辞林](https://itunes.apple.com/app/da-ci-lin/id299029654?mt=8)”程序针对不同的系统语言，加载屏幕（Loading Screen，也称Splash Screen）是不一样的。因为无可治愈的拖延症，导致我从来没有想着去研究一下为什么。今天闲来无事，简单的弄了个程序做了一下，解决了加载屏幕本地化的问题。问题的答案只有一句话：

加载屏幕(Default.png)和其他的资源文件一样，可以在Xcode里直接设置本地化的！

<del>（全文完）</del> 等等，既然写成博文了，怎么可以这么就结束了呢！好吧，那就略微详细一点，看一下在Xcode 4.6中如何一步一步地把加载屏幕本地化。

<!-- more -->

首先，我们用图形编辑(PS, Pixelmator等)工具，做两套语言的加载屏幕。简单起见，我们就用白底加居中的黑字(“US”和“CN”)的两套图片好了（文件在示例代码中有），每套图片要三个尺寸：Default.png(320x480)，Default@2x.png(640x960)以及Default-568h@2x.png(640x1136)。

做好图片之后，我们在Xcode里创建一个新项目名字可以叫做“Local Splash”，程序模板任意，简单起见，支持的设备只选iPhone。创建后，我们用“US”那套图片替换项目默认的纯黑色加载屏幕，作为默认语言的加载屏幕。

在Xcode的Project设置里，增加一个本地化语言Chinese(zh-Hans)。如下图所示：

{% img center /images/posts/local_splash_project_lang.png %}

设置完成后，再分别选择三个Default文件，点击右侧属性栏里的“Localize”按钮。这里选择“English”作为其语言。完成后，这三个Default文件会被移动到项目目录下的en.lproj目录中。

{% img center /images/posts/local_splash_us_loading.png %}

然后我们把三个“CN”的Default文件加入项目中。然后如上炮制。注意，这里选择“Chinese”作为其语言。完成后它们会被移入zh-Hans.lproj中。

{% img center /images/posts/local_splash_cn_loading.png %}

好了，到此，加载屏幕的本地化工作已经基本完成。为了保证看到我们的实验效果，我们在AppDelegate.m的`-application:didFinishLaunchingWithOptions:`的最前面加入下面一行：

```objc
	sleep(2);
```

这行代码的作用是让程序完成启动后睡2秒，不要在实际项目中加入这种代码！！！

下面就是在模拟器里跑一下看看效果了。按下cmd - r，看看加载屏幕显示的是不是和模拟器的语言一致（简体中文显示“CN”，英语和其他语言都显示“US”）；把模拟器的语言切换为其他语言再试一试，看看加载屏幕是不是已经变了。

好了，至此，实验完成。示例代码已经上传至[github](https://github.com/venj/Cocoa-blog-code/tree/master/Local%20Splash)。

(全文完)
