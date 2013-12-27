---
layout: post
title: "在Cocoa里玩转Dock图标"
date: 2011-09-03 0:35
comments: true
categories: [Dock, NSApplication, NSDockTile]
---

今天我们来看一看如何在Cocoa程序中自定义Dock图标。Dock图标的自定义主要包括四方面：

> * 加徽章（Badge）
> * 换图标
> * 隐藏和显示最小化时的图标徽章
> * 增加自定义Dock菜单

本文将对如何进行这四方面的自定义进行简单的介绍，并且在最后研究一下腾讯QQ for Mac独特的Badge机制。本文的示例代码使用了Acorn和腾讯QQ的图标做例子，请这两家厂商不要介意。
<!-- more --> 
**1 Dock徽章**

{% img center /images/posts/cocoa-dock-1.png Dock徽章 %}

给Dock图标加Badge的方法很常见，也很简单，用代码来说事吧。

``` objc
// Add badge
- (IBAction)addBadge:(id)sender {
    [[NSApp dockTile] setBadgeLabel:[NSString stringWithFormat:@"%d", random() % 11]];
}

// Remove badge
- (IBAction)removeBadge:(id)sender  {
    [[NSApp dockTile] setBadgeLabel:nil];
}
```

加Badge是用`NSDockTile`类的`-setBadgeLabel:`方法；移除Badge是把`badgeLabel`的内容设置为`nil`。这个方法在10.5以上都可以使用，除非需要兼容10.4以下的OSX－－现在似乎已经没有很大的必要了，或者是有其他特别的理由，否则真的很难拒绝使用Cocoa的这个简单的API调用。 ;)

**2 更换Dock图标**

{% img center /images/posts/cocoa-dock-2.png Dock图标 %}

有很多程序能够动态的修改程序图标，如，Adium和iCal。我们通过代码，我们可以在程序运行的时候动态设置Dock图标。修改图标有两种方法，一种是直接指定一个`NSImage`对象设置应用程序的图标；另一种是用一个自定义的View，来显示为Dock图标。

``` objc
// Customize Dock Icon
- (IBAction)changeDockIcon:(id)sender {
    [NSApp setApplicationIconImage:[NSImage imageNamed:@"Icon"]];
}

// Restore Dock Icon
- (IBAction)restoreDockIcon:(id)sender {
    [NSApp setApplicationIconImage:nil];
}

// Draw Dock Icon with Custom View
- (IBAction)changeDockIconWithCustomView:(id)sender {
    DockIconView *dockView = [[[DockIconView alloc] init] autorelease];
    [[NSApp dockTile] setContentView: dockView];
    [[NSApp dockTile] display];
}
```

用自定义View做图标不能自动刷新，所以如果Dock图标有所改变－－如加Badge时，可能需要手动通过`-display`方法刷新。

上面介绍的两种方法修改的程序图标会在程序退出之后还原为在`Info.plist`里指定的应用程序图标。要永久的改变程序图标（也就是退出程序的时候也能显示修改后的图标）的方法是创建Dock图标的插件。因为这个话题涉及Bundle相关的内容，在这里就不详述了，详情可以参考苹果的文档，以后有机会我会专门写文章介绍Dock图标插件的话题。

**3 隐藏和显示最小化时的图标徽章**

{% img center /images/posts/cocoa-dock-3.png 最小化图标徽章 %}

OSX的窗口在最小化的时候，在缩略图的右下角会显示一个程序图标徽章。我们可以通过程序来显示和隐藏最小化图标的徽章。方法很简单如下：

``` objc
// Show minimize icon badge
- (IBAction)showMinimizeBadge:(id)sender {
    [[self.window dockTile] setShowsApplicationBadge:YES];
}

// Remove minimize icon badge
- (IBAction)removeMinimizeBadge:(id)sender {
    [[self.window dockTile] setShowsApplicationBadge:NO];
}
```

**4 Dock菜单**

{% img center /images/posts/cocoa-dock-4.png Dock菜单 %}

Dock菜单有两种方法指定－－一种是直接在XIB中静态的设计菜单，然后通过Interface Builder关联到`NSApp`的`dockMenu`属性；另一种是通过`ApplicationDelegate`的`-applicationDockMenu:`方法，在程序中动态指定菜单。静态Dock菜单的指定方法如下：

{% img center /images/posts/cocoa-dock-5.png %}

所有操作通过Interface Builder就能完成。

动态指定Dock菜单的方法如下面的代码所示：

``` objc
- (NSMenu *)applicationDockMenu:(NSApplication *)sender {
    return self.dynamicMenu;
}
```

为了简便起见，我实际上并没有通过代码动态的创建菜单，上面的示例代码仅仅是引用了一个XIB里的菜单对象，通过`-applicationDockMenu:`方法返回。

**5 QQ风格的Dock徽章**

{% img center /images/posts/cocoa-dock-6.png QQ的Badge %}

你可能已经注意到了这个QQ图标的Badge和最上方的那个QQ图标的Badge有些不一样。没错，最上方的那个Badge是Lion的默认的风格的图标，是我做的一个示例，图标借用了腾讯QQ的图标。不过实际的QQ的Badge并不是使用OSX的系统Badge。具体腾讯怎么做的我也不是很清楚，不过我用自定义View用腾讯的空白Badge图标做了一个模仿的例子：

{% img center /images/posts/cocoa-dock-7.png QQ风格的Badge %}

我做的这个模仿示例，字体有些不太像，Badge文字的定位也有点不准确，不过我猜想腾讯的思路基本是和我类似，现在给出我写的实现。再次强调一下，这并非腾讯官方的方法，示例代码仅供参考：

``` objc
- (void)drawRect:(NSRect)dirtyRect
{
    // Drawing code here.
    
    // Draw Dock Icon
    NSImage *image = [NSImage imageNamed:@"qqbadge"];
    NSImageView *imageView = [[NSImageView alloc] initWithFrame:self.frame];
    imageView.image = image;
    [self addSubview:imageView];
    [imageView release];
    
    // Draw Badge Label
    NSRect labelFrame;
    CGFloat width = self.frame.size.width;
    CGFloat height = self.frame.size.height;
    labelFrame.origin = CGPointMake(width * 0.62, height * 0.60);
    labelFrame.size = CGSizeMake(width * 0.25, height * 0.25);
    self.label = [[NSTextField alloc] initWithFrame:labelFrame];
    self.label.backgroundColor = [NSColor clearColor];
    self.label.textColor = [NSColor whiteColor];
    self.label.drawsBackground = NO;
    self.label.stringValue = self.labelText;
    //在使用的时候，需要修改徽章Label，只需设置这个view对象的labelText属性即可
    NSFont *font = [NSFont fontWithName:@"Arial" size:32];
    [self.label setFont:font];
    [self.label sizeToFit];
    [self.label setBordered:NO];
    [self addSubview:self.label];
    [self.label release];
}
```

方法大致是，通过自定义View，绘制一个带空白Badge的图标，然后绘制一个图标徽章Label到Badge的位置，控制其大小，使其正好位于Label内部。

不过这种方法绘制的Badge有一个缺陷，那就是Badge的Label的长度不能太长，超过2个数字就不适合用这样的方法了。相比起来，用系统自带的Badge在Label很长的时候会自动拉长，更加灵活，而且只需一行代码，简单很多。可能腾讯这么做的目的最初是为了兼容Mac OS X 10.4才用了自定义View来绘制Badge；不过时代在进步，既然Cocoa已经提供了一个很简单的绘制Badge的API，腾讯是不是也应该更新一下了，况且，QQ for Mac现在的版本已经不再兼容10.6以下的系统了。

{% img center /images/posts/cocoa-dock-8.png %}

最后，本文的所有代码都已经包含在了一个示例项目[Play With Dock](https://github.com/venj/Cocoa-blog-code/tree/master/Play%20With%20Dock)中了，上图是运行的例子，代码已经[推送到Github上了](https://github.com/venj/Cocoa-blog-code)，有兴趣的可以看看，并且批评指正。

（全文完）
