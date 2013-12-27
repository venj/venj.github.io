---
layout: post
title: "强制Label(NSTextField)重绘"
date: 2011-04-13 22:57
comments: true
categories: [NSProgressIndicator, NSTextField]
---

今天在码代码的时候遇到了标签在修改了内容后没有重绘的问题，试了n种办法，查了老半天，结果终于找到了比较好的解决方法。  

首先，要说明，下面这样的方法是不能让Label(`NSTextField`)立刻重绘的：  

``` objc
[someLabel setStringValue:@"Some new text"];
[someLabel setNeedsDisplay:YES];
```
<!-- more --> 
正确的做法是：  

``` objc
[someLabel setStringValue:@"Some new text"];
[someLabel displayIfNeeded];
```

这两个方法看起来似乎差不多，不过仔细读读，还是能体会到差别的。`-setNeedDisplay:`是告诉`NSTextField`实例，我需要重绘了（但是什么时候重绘，则不一定是现在）。而`-displayIfNeeded`的意思是，如果需要（这里肯定是需要的，因为我们修改了标签的文字），马上重绘。  

这个做法参考了苹果官方文档的问答里的`NSProgressIndicator`的重绘有关的提问（[详细](http://developer.apple.com/library/mac/#qa/qa1473/_index.html)）。

（全文完）