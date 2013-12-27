---
layout: post
title: "Block的Retain Cycle的解决方法"
date: 2012-05-21 10:49
comments: true
categories: ["Block", "Retain Cycle"]
---

一个使用Block语法的实例变量，在引用另一个实例变量的时候，经常会引起retain cycle。这个问题在使用ASIHTTPRequest的block语法的时候会时不时的碰到。这个问题困扰了我这个小白很久。终于有一天，在Advanced Mac OS X Programming上，看到了这个问题的解决方案。

先用代码描述一下症状：
<!-- more -->
``` objc
/* ViewController.h */ 
#import <UIKit/UIKit.h>

typedef void (^ABlock)(void); //定义一个简单的Block

@interface ViewController : UIViewController {
    NSMutableArray *_items;
    ABlock _block;
}

@end

/* ViewController.m */ 

#import "ViewController.h"

@implementation ViewController

- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    _items = [[NSMutableArray alloc] init];
    _block = ^{
        [_items addObject:@"Hello!"]; //_block引用了_items，导致retain cycle。
    };
}

@end
```

Xcode在编译以上程序的时候会给出一个警告：Captureing 'self' strongly in this block is likely to lead to a retain cycle。原因是`_items`实际上是`self->items`。`_block`对象在创建的时候会被`retain`一次，因此会导致`self`也被`retain`一次。这样就形成了一个retain cycle。

解决方法就是，创建一个本地变量`blockSelf`，指向`self`，然后用结构体语法访问实例变量。代码如下：

``` objc
__block ViewController *blockSelf = self;
_block = ^{
    [blockSelf->_items addObject:@"Hello!"];
};
```

这么修改之后，`blockSelf`是本地变量，是弱引用，因此在`_block`被`retain`的时候，并不会增加retain count，所以retain cycle就解除了，Xcode也不再出现警告了，问题解决。

注：本文并非原创，详情请参阅[Advanced Mac OS X Programming](http://www.amazon.com/Advanced-Mac-OS-Programming-Guides/dp/0321706250)，第92页“Block Retain Cycles”。

(全文完)
