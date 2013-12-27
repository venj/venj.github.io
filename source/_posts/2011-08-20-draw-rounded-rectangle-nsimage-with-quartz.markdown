---
layout: post
title: "用Quartz绘制NSImage圆角图像"
date: 2011-08-20 0:42
comments: true
categories: [CGImage, NSImage, Round Corner]
---

今天偶尔看到了[用Quartz绘制`UIImage`圆角图像的源码的Snippet](http://www.wattz.net/snippets/iphone_round_corners/)，因此心血来潮，简单的改了几行，把它移植到NSImage上了。也正是因为研究这个问题，我才发现了[`NSImage`不能准确获得图片的实际像素大小的问题](/blog/get-real-size-of-nsimage/)。说实话，那个代码片断我也是一知半解，因此我只是依样画葫芦，也直接把源码附上，也不知道有没有更好的方法，仅供参考。
<!-- more --> 
我把这个源码改成了`NSImage`的一个Category，从而方便日后自己使用。代码如下**（更新了代码，修复了一个提前`release`的问题）**：

``` objc
/* NSImage+RoundCorner.h */
#import <Foundation/Foundation.h>
/* C Prototypes */
void addRoundedRectToPath(CGContextRef context, CGRect rect, float ovalWidth, float ovalHeight);

@interface NSImage(RoundCorner)
- (NSImage *)roundCornersImageCornerRadius:(NSInteger)radius;
@end


/* NSImage+RoundCorner.m */
#import "NSImage+RoundCorner.h"

@implementation NSImage (RoundCorner)

void addRoundedRectToPath(CGContextRef context, CGRect rect, float ovalWidth, float ovalHeight)
{
    float fw, fh;
    if (ovalWidth == 0 || ovalHeight == 0) {
        CGContextAddRect(context, rect);
        return;
    }
    CGContextSaveGState(context);
    CGContextTranslateCTM (context, CGRectGetMinX(rect), CGRectGetMinY(rect));
    CGContextScaleCTM (context, ovalWidth, ovalHeight);
    fw = CGRectGetWidth (rect) / ovalWidth;
    fh = CGRectGetHeight (rect) / ovalHeight;
    CGContextMoveToPoint(context, fw, fh/2);
    CGContextAddArcToPoint(context, fw, fh, fw/2, fh, 1);
    CGContextAddArcToPoint(context, 0, fh, 0, fh/2, 1);
    CGContextAddArcToPoint(context, 0, 0, fw/2, 0, 1);
    CGContextAddArcToPoint(context, fw, 0, fw, fh/2, 1);
    CGContextClosePath(context);
    CGContextRestoreGState(context);
}

- (NSImage *)roundCornersImageCornerRadius:(NSInteger)radius {
    int w = (int) self.size.width;
    int h = (int) self.size.height;
    
    CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();
    CGContextRef context = CGBitmapContextCreate(NULL, w, h, 8, 4 * w, colorSpace, kCGImageAlphaPremultipliedFirst);
    
    CGContextBeginPath(context);
    CGRect rect = CGRectMake(0, 0, w, h);
    addRoundedRectToPath(context, rect, radius, radius);
    CGContextClosePath(context);
    CGContextClip(context);
    
    CGImageRef cgImage = [[NSBitmapImageRep imageRepWithData:[self TIFFRepresentation]] CGImage];
    
    CGContextDrawImage(context, CGRectMake(0, 0, w, h), cgImage);
    
    CGImageRef imageMasked = CGBitmapContextCreateImage(context);
    CGContextRelease(context);
    CGColorSpaceRelease(colorSpace);
    
    NSImage *tmpImage = [[NSImage alloc] initWithCGImage:imageMasked size:self.size];
    NSData *imageData = [tmpImage TIFFRepresentation];
    NSImage *image = [[NSImage alloc] initWithData:imageData];
    [tmpImage release];
    
    return [image autorelease];
}
@end
```

测试效果如下：

{% img center /images/posts/round-corner-nsimgage.png %}

本例的源代码已经上传到[github](https://github.com/venj/Cocoa-blog-code/tree/master/Round%20Corner%20Image)，顺便也测试了一下[前文](/blog/get-real-size-of-nsimage/)所述的问题，你可以顺便简单的看一下。于是乎，iOS上也可以如法炮制一个`UIImage`的Catagory，方便使用。我在这里就不赘述了，有兴趣的可以自己试着改改。 :D

(全文完)
