---
layout: post
title: "在iOS上用Quartz绘制圆角矩形"
date: 2011-08-06 0:28
comments: true
categories: [Core Graphics, Quartz, UIKit]
---

Bezels是Mac OS X下，苹果私有的UI控件，用来提示用户某些信息。不过iOS下，就有很多第三方的Bezels风格的提示控件实现了。相信大家也见过很多了。下图左侧是OSX的提示音量变化的Bezels，右侧是iOS上的Bezels风格的进度提示。

{% img center /images/posts/quartz-1.png %}

在iOS上做Bezels的思路很简单，无非就是在`UIView`里绘制一个半透明的圆角矩形，然后加入其他的`SubView`。因为没有现成的绘制圆角矩形的API可用，所以我们来自己绘制一个——反正是为了学习嘛。

新建项目，添加一个`UIView`的子类，修改`drawRect`方法，如下：
<!-- more --> 
``` objc
- (void)drawRect:(CGRect)rect
{
    CGFloat width = rect.size.width;
    CGFloat height = rect.size.height;
    // 简便起见，这里把圆角半径设置为长和宽平均值的1/10
    CGFloat radius = (width + height) * 0.05;

    // 获取CGContext，注意UIKit里用的是一个专门的函数
    CGContextRef context = UIGraphicsGetCurrentContext();
    // 移动到初始点
    CGContextMoveToPoint(context, radius, 0);

    // 绘制第1条线和第1个1/4圆弧
    CGContextAddLineToPoint(context, width - radius, 0);
    CGContextAddArc(context, width - radius, radius, radius, -0.5 * M_PI, 0.0, 0);

    // 绘制第2条线和第2个1/4圆弧
    CGContextAddLineToPoint(context, width, height - radius);
    CGContextAddArc(context, width - radius, height - radius, radius, 0.0, 0.5 * M_PI, 0);

    // 绘制第3条线和第3个1/4圆弧
    CGContextAddLineToPoint(context, radius, height);
    CGContextAddArc(context, radius, height - radius, radius, 0.5 * M_PI, M_PI, 0);

    // 绘制第4条线和第4个1/4圆弧
    CGContextAddLineToPoint(context, 0, radius);
    CGContextAddArc(context, radius, radius, radius, M_PI, 1.5 * M_PI, 0);
    
    // 闭合路径
    CGContextClosePath(context);
    // 填充半透明黑色
    CGContextSetRGBFillColor(context, 0.0, 0.0, 0.0, 0.5);
    CGContextDrawPath(context, kCGPathFill);
}
```

其实这用到的只是些很基本的Quartz绘图函数而已。不过，我们顺利的绘制出了半透明，黑色的，圆角Bezel风格的View。下面就是一个测试的例子——绘制长方形的也行哦。 :P

{% img center /images/posts/quartz-2.png %} 

当然，这只是个基础，但是不管怎样，有了这个很好的开始，自己做一个Bezels风格ViewController就很方便了。如果你要在实际的项目中使用Bezels风格的View，但是又不想自己麻烦动手写，网上已经有很多开源的Bezels实现，自己找一找吧。 :)

（全文完）
