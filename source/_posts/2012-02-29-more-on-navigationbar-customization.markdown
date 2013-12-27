---
layout: post
title: "再谈iOS 4下NavigationBar的自定义"
date: 2012-02-29 10:30
comments: true
categories: [UINavigationBar, iOS 4]
---

iOS 5已经有了很方便的自定义`UINavigationBar`的方法，但是iOS 4现在还远未完全被iOS 5取代，因此，在做程序开发的时候必须要考虑对iOS 4的兼容。因此，要自定义`UINavigationBar`还不能简单的直接用iOS 5中才有的自定义API。

关于这个话题，我曾经写过[一篇日志](http://cocoa.venj.me/blog/custom-navbar-background/)，但是后来发现那篇日志里面的实现方法有诸多缺陷。因此我后来对那篇文章做过部分删改。这两天，我再次研究了一下NavigationBar的自定义，总算找到了一个近乎完美的解决方法，因此，我决定写出来和大家分享一下。

本文中的实现方法很大程度上参考了[这篇文章](http://idevrecipes.com/2011/01/12/how-do-iphone-apps-instagramreederdailybooth-implement-custom-navigationbar-with-variable-width-back-buttons/)，不过我这里介绍的实现方法在原方法的基础上加入了对设备旋转的支持。

方法的核心思想是创建一个`UINavigationBar`的子类，让这个子类支持背景图，然后在项目中用自定义类替换`UINavigationController`的`navgationBar`。不过有一个问题是，`UINavigationController`的`navgationBar`属性是一个只读属性，因此我们只能在xib中将`navgationBar`的类改成我们的自定义类。

我还没有找到适合StoryBoard的无xib的解决方法，因此我在上面说这个方法是“近乎完美”。不过既然是部署到iOS 4，那项目不采用StoryBoard也不算特别反人类。好了，下面开始代码说话（注：代码用了ARC）：

<!-- more -->

``` objc
/* VCCustomNavigationBar.h */

#import <UIKit/UIKit.h>

@interface VCCustomNavigationBar : UINavigationBar
@property (nonatomic, strong) UIImageView *navigationBarBackgroundImage;
@property (nonatomic, strong) UIImage *landscapeBarBackground;
@property (nonatomic, strong) UIImage *portraitBarBackground;
- (void)setBackgroundForDeviceOrientation:(UIDeviceOrientation)orientation;
- (void)clearBackground;
@end

/* VCCustomNavigationBar.m */

#import "VCCustomNavigationBar.h"

@implementation VCCustomNavigationBar
@synthesize navigationBarBackgroundImage = _navigationBarBackgroundImage;
@synthesize landscapeBarBackground = _landscapeBarBackground;
@synthesize portraitBarBackground = _portraitBarBackground;

- (id)initWithFrame:(CGRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {
        // Initialization code
    }
    return self;
}

- (void)awakeFromNib {
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(changeBackgroundImage:) name:UIDeviceOrientationDidChangeNotification object:NULL];
}

- (void)drawRect:(CGRect)rect
{
    if (self.navigationBarBackgroundImage)
        [self.navigationBarBackgroundImage.image drawInRect:rect];
    else
        [super drawRect:rect];
}

- (void)setBackgroundForDeviceOrientation:(UIDeviceOrientation)orientation;
{
    self.navigationBarBackgroundImage = [[UIImageView alloc] initWithFrame:self.frame];
    if ((orientation == UIDeviceOrientationLandscapeLeft) 
        || (orientation == UIDeviceOrientationLandscapeRight)) {
        self.navigationBarBackgroundImage.image = self.landscapeBarBackground;
    }
    else if (orientation == UIDeviceOrientationPortrait) {
        self.navigationBarBackgroundImage.image = self.portraitBarBackground;
    }
    [self setNeedsDisplay];
}

- (void)clearBackground
{
    self.navigationBarBackgroundImage = nil;
    [self setNeedsDisplay];
}

- (void)dealloc {
    [[NSNotificationCenter defaultCenter] removeObserver:self];
}

- (void)changeBackgroundImage:(NSNotification *)notification {
    UIDeviceOrientation currentOrientation = [[UIDevice currentDevice] orientation];
    if (currentOrientation == UIDeviceOrientationPortraitUpsideDown) {
        return;
    }
    [self setBackgroundForDeviceOrientation:currentOrientation];
}

@end

```

在需要修改`NavigationBar`的`ViewController`中，在`-viewDidLoad`方法中设置自定义背景图：

``` objc
- (void)viewDidLoad
{
    [super viewDidLoad];

    //设置合适的NavBar背景(按钮)颜色，配合背景图
    self.navigationController.navigationBar.tintColor = [UIColor brownColor];
    
    //自定义NavBar背景
    VCCustomNavigationBar *customBar = (VCCustomNavigationBar *)self.navigationController.navigationBar;
    customBar.landscapeBarBackground = [UIImage imageNamed:@"landscapeBarBackground"];
    customBar.portraitBarBackground = [UIImage imageNamed:@"portraitBarBackground"];
    [customBar setBackgroundForDeviceOrientation:[[UIDevice currentDevice] orientation]];

    //其他自定义代码
}
```

效果图如下：

{% img center /images/posts/navbar_bg_portrait.png %}

{% img center /images/posts/navbar_bg_landscape.png %}

我已经把项目推送到[github](https://github.com/venj/Cocoa-blog-code/tree/master/Custom%20NavBar)上了，有兴趣的可以拉下来研究一下。因为现在Xcode 4生成的模版已经默认不使用MainWindow.xib了，因此，你在测试的时候可能要自己创建一个Window.xib，并且自己创建所有对象，并连接Outlet等。

(全文完)