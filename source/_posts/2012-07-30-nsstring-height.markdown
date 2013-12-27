---
layout: post
title: "关于多行NSString的高度"
date: 2012-07-30 09:01
comments: true
categories: [NSString, AppKit, UIKit]
---

有时候，我们需要动态地得到一个多行字符串的高度。比如，在TableView中，如果我们允许`UILabel`/`NSTextField`多行显示，而TableView的Delegate就需要知道Cell的高度，这时，我们需要根据TableView呈现的内容，动态地计算多行字符串的高度，以确定Cell的高度。

这样的需求，在UIKit中实现起来相对比较直观。我们就先看看UIKit中的情况。

`NSString`的`UIStringDrawing`这个Catagory中，有一个非常方便调用的方法：`- sizeWithFont:forWidth:lineBreakMode:`。看下面的示例代码：

```objc
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    ...
    cell.textLabel.lineBreakMode = UILineBreakModeWordWrap; //设置换行模式
    cell.textLabel.numberOfLines = 0; //允许多行
    ...
}

- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
    ...
    NSString cellText = ...;
    CGSize maximumSize = CGSizeMake(280., 9999.); //指定一个宽度为实际宽度，高度为极大指的尺寸
    CGSize labelSize = [cellText sizeWithFont:[UIFont systemFontOfSize:14.] constrainedToSize:maximumSize lineBreakMode:UILineBreakModeWordWrap]; //调用方法，获取字符串的尺寸

    return labelSize.height + SOME_SPECE_FOR_OTHER_CONTROL; //返回字符串的高度，加上一些自由空间的高度或其他空间所占的高度。
}
```

遗憾的是，AppKit里没有对应于上述调用的简便方法。这时候，苹果的文档就发挥作用了。在[Text Layout Programming Guide](http://developer.apple.com/documentation/Cocoa/Conceptual/TextLayout/TextLayout.html)中有一段示例代码，展示了如何使用`NSString`以及一些其他的类，来实现类似的功能。下面是我自己用在NSTableView中的的一段代码，照抄了文档中的示例代码，如下：

```objc
- (CGFloat)tableView:(NSTableView *)tableView heightOfRow:(NSInteger)row {
    NSString *statusText = [[_statusMessages objectAtIndex:row] text];
    return [self heightForStringDrawing:statusText font:[NSFont systemFontOfSize:12] width:tableView.frame.size.width] + 8;
}

//苹果官方文档中的示例方法
- (CGFloat)heightForStringDrawing:(NSString *)myString font:(NSFont *)myFont width:(CGFloat)myWidth {
    NSTextStorage *textStorage = [[NSTextStorage alloc] initWithString:myString];
    NSTextContainer *textContainer = [[NSTextContainer alloc] initWithContainerSize: NSMakeSize(myWidth, FLT_MAX)];
    NSLayoutManager *layoutManager = [[NSLayoutManager alloc] init];
    
    [layoutManager addTextContainer:textContainer];
    [textStorage addLayoutManager:layoutManager];
    [textStorage addAttribute:NSFontAttributeName value:myFont range:NSMakeRange(0, [textStorage length])];
    [textContainer setLineFragmentPadding:0.0];
    
    [layoutManager glyphRangeForTextContainer:textContainer];
    return [layoutManager usedRectForTextContainer:textContainer].size.height;
}
```

上述代码中涉及了`NSTextStorage`，`NSTextContainer`，`NSLayoutManager`等多个比较复杂的方法，我对这些类也不是很熟悉，因此我就不详细注释了，有兴趣的同学可以查阅这些类的文档。你也可以将上述方法稍作修改，自己写一个`NSString`的Catagory，方便以后使用。你甚至可以基于上述方法，写一个/一组对应于`NSString(UIStringDrawing)`里计算字符串尺寸的快捷方法。

还是那个老梗，任何时候，RTFM总能给人带来惊喜和收获。

(全文完)