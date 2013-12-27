---
layout: post
title: "用Cocoa实现用Mac的摄像头拍照"
date: 2011-04-23 23:10
comments: true
categories: [ImageKit, iSight, Take Photo]
---

我一直有一种错觉，似乎在Mac上通过Cocoa API，用iSight拍照会有点困难。但是我彻底错了！用Cocoa拍照非常非常简单。甚至比Cocoa Touch拍照还简单！

首先，我们来看看测试效果：

{% img center /images/posts/picture-taker.png %}
<!-- more --> 
{% img center /images/posts/picture-taker-effect.png %}

{% img center /images/posts/picture-taker-effect-2.png %}

言归正传，我们开始开始拍照。。。。不对，是开始写拍照的代码。。。Orz

``` objc
- (void)applicationDidFinishLaunching:(NSNotification *)aNotification
{
    // 初始化一个pictureTaker对象
    pictureTaker = [IKPictureTaker pictureTaker];
    /* 下面两行代码对pictureTaker进行一些设置，比如增加拍照效果的支持，以及设置拍摄的照片的最大尺寸 */
    //[pictureTaker setValue:[NSNumber numberWithBool:YES] forKey:IKPictureTakerShowEffectsKey];
    //[pictureTaker setValue:[NSValue valueWithSize:NSMakeSize(800.0, 600.0)] forKey:IKPictureTakerOutputImageMaxSizeKey];
}
// Picture taker的回调方法。
- (void) pictureTakerDidEnd:(IKPictureTaker *)picker
                 returnCode:(NSInteger)code
                contextInfo:(void*)contextInfo
{
    // 获取图片，传递给Image Well。
    NSImage *image = [picker outputImage];
    [imageView setImage:image];
}
// 这里，用主窗口sheet的方式打开拍照窗口。
- (IBAction)takePicture:(id)sender {
    [pictureTaker beginPictureTakerSheetForWindow:[self window] withDelegate:self didEndSelector:@selector(pictureTakerDidEnd:returnCode:contextInfo:) contextInfo:nil];
}
```

就是这么简单！不错吧！

这里，我们用到了Image Kit里的方法和类，Image Kit是一个处理图像的Objective-C API，相当简单而强大。今天只是用到了其中的`IKPictureTaker`一个类，其它的类等以后需要的时候继续探索。Image Kit是Quartz框架的一个子框架，因此在编译的时候需要连接到Quartz，并且要包含`Quartz.h`这个头文件。

更多资料，参考苹果自家的[文档](http://developer.apple.com/library/mac/documentation/GraphicsImaging/Conceptual/ImageKitProgrammingGuide/)。

（全文完）