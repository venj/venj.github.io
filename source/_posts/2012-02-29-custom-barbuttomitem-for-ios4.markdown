---
layout: post
title: "在iOS 4中自定义BarButtonItem"
date: 2012-02-29 15:08
comments: true
categories: [UIBarButtonItem, UINavigationController]
---

在iOS 5中已经有了自定义`UIBarButtonItem`背景的API。但是在iOS 4中，自定义BarButtonItem背景就复杂很多了。其中最典型也最容易实现的一种是方法是试用`UIBarButtonItem`的`-initWithCustomView:`方法，传递一个自定义`UIButton`对象进去。

在深入讨论自定义`UIBarButtonItem`，让我们来先解决自定义`UIButton`的一个问题：那就是Button的标题的长度是变化的，我们应该通过什么方法让背景图片适应标题长度的变化呢？

下面是解决方案：

<!-- more -->

``` objc
if ([image respondsToSelector:@selector(resizableImageWithCapInsets:)]) {
    // iOS 5中的方法。
    buttonImage = [image resizableImageWithCapInsets:UIEdgeInsetsMake(0, leftcap, 0, rightcap)];
}
else {
    // iOS 4-中的方法。该方法在iOS 5中已经标注为“Depreciated”。
    buttonImage = [image stretchableImageWithLeftCapWidth:leftcap topCapHeight:0];
}
```

这个解决方案的核心是`-stretchableImageWithLeftCapWidth:topCapHeight:`（或iOS 5中的`- resizableImageWithCapInsets:`）方法。这个方法能够根据指定的左右两侧“帽子”的尺寸（当然，上下拉伸也可以），拉伸图片的中间部分。具体效果你可以亲自试验一下。

好了，回到自定义`UIBarButtonItem`的问题上来。既然`UIButton`背景的问题解决之后，我们就可以创建基于`UIButton`对象的`UIBarButtonItem`了：

``` objc
// 用来被拉伸的图片
UIImage *originalImage = [UIImage imageNamed:@"backButton"];
UIImage *buttonImage;
// 拉伸图片
if ([[UIImage class] respondsToSelector:@selector(resizableImageWithCapInsets:)]) {
    buttonImage = [originalImage resizableImageWithCapInsets:UIEdgeInsetsMake(0, 13, 0, 8)];
}
else {
    buttonImage = [originalImage stretchableImageWithLeftCapWidth:13 topCapHeight:0];
}
// 创建自定义按钮
UIButton *backButton = [UIButton buttonWithType:UIButtonTypeCustom];
[backButton setBackgroundImage:buttonImage forState:UIControlStateNormal];
NSString *title = @"Back";
[backButton setTitle:title forState:UIControlStateNormal];
UIFont *font = [UIFont boldSystemFontOfSize:12];
backButton.titleLabel.font = font;
CGSize labelSize = [title sizeWithFont:font];
// 模拟系统按钮，设置文字阴影
backButton.titleLabel.shadowColor = [UIColor grayColor];
backButton.titleLabel.shadowOffset = CGSizeMake(0.0, -1.0);
// 设置按钮的Frame，宽度为文字的宽度加上拉伸图宽度的和
backButton.frame = CGRectMake(0, 0, labelSize.width + buttonImage.size.width, buttonImage.size.height);
// 设置按钮的target和action
[backButton addTarget:self action:@selector(back) 
     forControlEvents:UIControlEventTouchUpInside];
//创建UIBarButtonItem
self.navigationItem.leftBarButtonItem = [[UIBarButtonItem alloc] 
                                         initWithCustomView:backButton];

```

这就是实现自定义`UIBarButtonItem`对象的核心了。我写了一个`UIBarButtonItem`的subclass，有点粗糙，但是能够实现上述的所有功能，并且能够响应设备方向的变化对按钮的宽度作出调整。用法很简单：

``` objc
VCTranslucentBarButtonItem *item = [[VCTranslucentBarButtonItem alloc] initWithType:VCTranslucentBarButtonItemTypeBackward title:@"Test Button" target:self action:@selector(buttonClicked)];
self.navigationItem.leftBarButtonItem = item;
```

完整的源码我就不在本文中贴出来了，反正已经推送到github上了。效果如下图所示：

{% img center /images/posts/barbuttonportrait.png %}

{% img center /images/posts/barbuttonlandscape.png %}

上图中显示的是`DetailViewController`的自定义后退按钮，注意颜色上和`MasterViewController`中按钮的区别。因为用的按钮素材的问题，效果图未能完整复制系统的按钮样式，不过这已经足够说明问题了。实战中，你可以让设计师做更好，更美观的按钮。

有兴趣的可以把[github](https://github.com/venj/Cocoa-blog-code/tree/master/Custom%20NavBar)上的源码拉下来看看。本文和前一篇文章的源码在同一个项目中。

注：本文所有的代码都用了ARC，以后的文章中将不再特别说明。如果你看到本博客的示例代码没有`release`，那么基本上都是用了ARC。

(全文完)