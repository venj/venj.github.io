---
layout: post
title: "在iOS程序中使用自定义字体"
date: 2012-01-10 10:58
comments: true
categories: ["字体"]
---

好久没有更新这里了，今天开始慢慢恢复更新。好了，废话不多说，开始讲正题。今天的话题其实也很简单，那就是在iOS中使用自定义字体。

虽然iOS自带的字体通常已经足够我们使用了，但是，对于某些特殊程序来说，比如，电子书程序，可能需要用到一些特殊的字体，或者需要用更好看的字体来达到我们想要的显示效果。不过，iOS是不支持系统级安装新字体的。不过在iOS 3.x中，我们已经可以用一个很简单的方法来使用自定义字体了。

我们来创建一个简单的iOS项目来做演示。在界面上拖入一个`UITextView`，在代码中创建一个`IBOutlet`指向这个`UITextView`。我们从OSX中找一个iOS下没有的字体，比如：PTSans.ttc（这个也是Octopress模版的默认字体，我很喜欢这个字体），拖放到项目中。

打开info.plist，增加一个新的Array类型的键，键名设置为UIAppFonts（Fonts provided by application），增加字体的**文件名**：“PTSans.ttc“。

然后我们就可以在程序中调用这个字体了：
<!-- more -->
``` objc
- (void)viewDidLoad
{
    [super viewDidLoad];
    // 在viewDidLoad方法中设置字体。
    self.tv.font = [UIFont fontWithName:@"PT Sans" size:20];
}
```

下面是在iOS模拟器中的显示效果：

{% img center /images/posts/customfont.png %}

还不错吧。 :)

最后提醒一点，在调用字体的时候，要使用字体名。**字体名不是文件名**，而是字体的Family Name。Family Name可以在Font Book中查看。

本文的示例代码已经推送到[Github](https://github.com/venj/Cocoa-blog-code/tree/master/iPhoneCustomFont)上了。在编译示例代码前，请先复制PTSans.ttc到`iPhoneCustomFont/iPhoneCustomFont`目录下。

(全文完)
