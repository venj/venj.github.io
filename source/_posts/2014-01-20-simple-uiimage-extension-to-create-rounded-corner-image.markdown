---
layout: post
title: "一个简单的创建圆角矩形图像的UIImage扩展实现"
date: 2014-01-20 13:58:15 +0800
comments: true
categories: [UIImage, 圆角]
---

在iOS开发中经常需要用到圆角图像。简单搜索一下就能找到很多创建圆角图像的实现代码。我在[Stack Overflow](http://stackoverflow.com/questions/10563986/uiimage-with-rounded-corners)上找到了一段代码，略微修改了一下，写了个简单的Catagory方法，可以用来创建圆角图像。代码如下：

<!-- more -->

```objc
/* UIImage+RoundedRect.h*/
#import <UIKit/UIKit.h>

@interface UIImage (RoundedCorner)
- (UIImage *)roundedCornerImageWithCornerRadius:(CGFloat)cornerRadius;
@end

/* UIImage+RoundedRect.m*/

#import "UIImage+RoundedCorner.h"

@implementation UIImage (RoundedCorner)

- (UIImage *)roundedCornerImageWithCornerRadius:(CGFloat)cornerRadius {
	CGFloat w = self.size.width;
	CGFloat h = self.size.height;
	CGFloat scale = [UIScreen mainScreen].scale;
    // 防止圆角半径小于0，或者大于宽/高中较小值的一半。
    if (cornerRadius < 0)
        cornerRadius = 0;
    else if (cornerRadius > MIN(w, h))
        cornerRadius = MIN(w, h) / 2.;

    UIImage *image = nil;
    CGRect imageFrame = CGRectMake(0., 0., w, h);
    UIGraphicsBeginImageContextWithOptions(self.size, NO, scale);
    [[UIBezierPath bezierPathWithRoundedRect:imageFrame cornerRadius:cornerRadius] addClip];
    [self drawInRect:imageFrame];
    image = UIGraphicsGetImageFromCurrentImageContext();
	UIGraphicsEndImageContext();
    
    return image;
}

@end
```

把这个Catagory添加到项目中之后，直接对`UIImage`对象调用`- roundedCornerImageWithCornerRadius:`方法即可。以上实现并非最高效，最佳的实现，但是有时候Quick and Dirty的方法已经足够好了，是吧。（天音：你就自我安慰吧。）

(全文完)
