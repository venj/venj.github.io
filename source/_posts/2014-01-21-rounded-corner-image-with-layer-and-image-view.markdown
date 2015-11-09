---
layout: post
title: "用ImageView和Layer实现图像的圆角显示"
date: 2014-01-21 09:13:01 +0800
comments: true
categories: [UIImageView, CALayer]
---

昨天写过一个[博文](/blog/simple-uiimage-extension-to-create-rounded-corner-image/)，介绍了创建圆角矩形图像的方法。现在，我再介绍一个**“显示”**圆角图像的方法。

这种方法利用的是`UIImageView`的`layer`的属性，实现把图像显示成圆角，而无需对图像本身进行处理。在开发中，大部分情况下，我们只需要“显示”圆角，而不是“得到”圆角图像。不是么。好了，先看代码：

<!-- more -->

``` objc
UIImageView *imageView = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"sample"]];
//设置圆角半径，只要是imageView的宽/高中较小的一个值的一半或更小的值就可以
imageView.layer.cornerRadius = 10.0;
imageView.layer.masksToBounds = YES;
```

简单解释一下。每个`UIView`对象都有一个`layer`属性，这个`layer`属性实际上是一个`CALayer`对象。这个对象负责把`UIView`对象显示到屏幕上。而`layer`则有一个`cornerRadius`属性，用来指定`layer`的边框圆角。但是光设定这个属性是不够的，因为`layer`默认会把图像超出边框的部分也显示出来，所以必须指定`masksToBounds`属性为`YES`，让`layer`切掉边框外的部分。最终实现了图像的圆角显示。

这种方法不需要处理图像本身，如果只是为了显示圆角，这是一种非常高效的方法——所以除非你真的需要得到圆角图像对象本身。

（全文完）
