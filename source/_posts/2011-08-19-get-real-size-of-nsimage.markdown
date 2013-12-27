---
layout: post
title: "获取NSImage图片的准确大小"
date: 2011-08-19 23:58
comments: true
categories: [NSImage, UIImage]
---

今天在测试代码的时候发现了一个诡异的问题，那就是`NSImage`加载图片的时候无法准确获得图片的大小。

最初的代码是这样的：

``` objc
NSImage *image = [NSImage imageNamed:@"image"];
NSLog(@"%f, %f", image.size.width, image.size.height);
//终端Log到的图片的尺寸是：185.250000, 106.500000，而实际的大小是274.0, 142.0。
//image.size = NSMakeSize(247.0, 142.0); //强制指定尺寸能够解决问题
```

我用一个Image Well来显示图片的时候，发现图片居然比实际的要小！我试着`NSLog`图片的大小，发现`NSImage`加载的图片的`size`根本就是错的！我根据图片的实际大小，强制设置了图片的尺寸，图片就显示正常了，而且也没有被拉伸的痕迹。这说明图片数据在加载后并没有损耗，只是图片的`size`属性出错了，因此，强制设置`size`属性，图片就能正常显示了。
<!-- more --> 
在搜索了一番之后，发现这个问题似乎和[图片的DPI有关](http://stackoverflow.com/questions/4748846/nsimage-dpi-question)。如果图片的DPI不是72，`NSImage`加载图片后`size`属性就会出错。我在iOS上用同样的方法做了测试，发现`UIImage`则没有类似的问题。虽然我对图片的DPI的概念也是一知半解，但是，像72 DPI这种应该算是历史的包袱了，毕竟Cocoa是一个非常古老的框架了。

虽然在这里，人肉设置图片大小能够解决问题，不过总不能在以后的代码中都人肉设置每张图片的尺寸吧。总有什么准确的方法来获取图片的真实像素尺寸的。然后就找到了[Stack Overflow](http://stackoverflow.com/)里的[这个问题](http://stackoverflow.com/questions/2190027/nsimage-acting-weird)，解决方法是这样的：

``` objc
// 不要用这个方法，这个方法并不一定总能成功。
NSBitmapImageRep *rep = [[image representations] objectAtIndex: 0];
NSSize size = NSMakeSize([rep pixelsWide], [rep pixelsHigh]);
[image setSize: size];
```

我试了一下这个方法，确实能够解决我的问题。不过这个方法下面，两个开发人员的对话很有启发，其中一人提到了一个问题，那就是`NSImage`可能并不存在`NSBitmapImageRep`－－这并不稀奇，比如，用[Mac的摄像头拍下的图片](/blog/take-photo-with-isight-camera/)就没有！因此，一定有更好的方法。于是，我顺藤摸瓜，又在Stack Overflow上找到了[另一个更加靠谱的方法](http://stackoverflow.com/questions/4748846/nsimage-dpi-question)：

``` objc
NSImage *image = [NSImage imageNamed:@"image"];
NSBitmapImageRep *rep = [NSBitmapImageRep imageRepWithData:[image TIFFRepresentation]];
NSSize size = NSMakeSize([rep pixelsWide], [rep pixelsHigh]);
[image setSize: size];
```

这种方法其实在原理上和之前说的那个有缺陷的方法并没有非常本质的不同。只是这种方法是创建了一个`NSBitmapImageRep`，而不是从图片中获取一个。这样，就不会存在图片对象中无法获取`NSBitmapImageRep`的问题了。

至此，问题完美解决。不过这个问题提醒了我，不要对Cocoa的内建对象想当然，一定要注意Cocoa框架中的陷阱。

(全文完)
