---
layout: post
title: "Objective-C中单例模式的实现"
date: 2012-02-07 20:29
comments: true
categories: ["单例模式", "Objective-C", "Cocoa", "Cocoa Touch"]
---

单例模式在Cocoa和Cocoa Touch中非常常见。比如这两个，`[UIApplication sharedApplication]`和`[NSApplication sharedApplication]`，大家应该都见过。但是我们应该如何在代码中实现一个单例模式呢？

如果你对苹果的文档很熟悉的话，你一定知道，在[Cocoa Foundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)中有一段[实现单例模式的示例代码](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaObjects/CocoaObjects.html#//apple_ref/doc/uid/TP40002974-CH4-SW32)。大致如下：
<!-- more -->
``` objc
/* Singleton.h */
#import &lt;Foundation/Foundation.h&gt;

@interface Singleton : NSObject
+ (Singleton *)instance;
@end

/* Singleton.m */
#import "Singleton.h"
static Singleton *instance = nil;

@implementation Singleton

+ (Singleton *)instance {
    if (!instance) {
        instance = [[super allocWithZone:NULL] init];
    }
    return instance;
}

+ (id)allocWithZone:(NSZone *)zone {
    return [self instance];
}

- (id)copyWithZone:(NSZone *)zone {
    return self;
}

- (id)init {
    if (instance) {
        return instance;
    }
    self = [super init];
    return self;
}

- (id)retain {
    return self;
}

- (oneway void)release {
    // Do nothing
}

- (id)autorelease {
    return self;
}

- (NSUInteger)retainCount {
    return NSUIntegerMax;
}

@end
```

这是一种很标准的Singleton实现，中规中矩。不过这种实现并不是线程安全的。所以各路大神都各显神威，给出了多种单例模式的实现。

Matt Gallagher在[博客](http://cocoawithlove.com/2008/11/singletons-appdelegates-and-top-level.html)中放出了一个Macro，用来实现单例模式。虽然是一个宏定义的代码，但是具体实现还是很清楚的。代码如下：

``` objc
//
//  SynthesizeSingleton.h
//  CocoaWithLove
//
//  Created by Matt Gallagher on 20/10/08.
//  Copyright 2009 Matt Gallagher. All rights reserved.
//
//  Permission is given to use this source code file without charge in any
//  project, commercial or otherwise, entirely at your risk, with the condition
//  that any redistribution (in part or whole) of source code must retain
//  this copyright and permission notice. Attribution in compiled projects is
//  appreciated but not required.
//

#define SYNTHESIZE_SINGLETON_FOR_CLASS(classname) \
 \
static classname *shared##classname = nil; \
 \
+ (classname *)shared##classname \
{ \
    @synchronized(self) \
    { \
        if (shared##classname == nil) \
        { \
            shared##classname = [[self alloc] init]; \
        } \
    } \
     \
    return shared##classname; \
} \
 \
+ (id)allocWithZone:(NSZone *)zone \
{ \
    @synchronized(self) \
    { \
        if (shared##classname == nil) \
        { \
            shared##classname = [super allocWithZone:zone]; \
            return shared##classname; \
        } \
    } \
     \
    return nil; \
} \
 \
- (id)copyWithZone:(NSZone *)zone \
{ \
    return self; \
} \
 \
- (id)retain \
{ \
    return self; \
} \
 \
- (NSUInteger)retainCount \
{ \
    return NSUIntegerMax; \
} \
 \
- (void)release \
{ \
} \
 \
- (id)autorelease \
{ \
    return self; \
}
```

然而，eschaton则觉得这些实现都太繁琐了，他给出的实现如下：

``` objc
@interface SomeManager : NSObject
+ (id)sharedManager;
@end

/* 非线程安全的实现 */
@implementation SomeManager

+ (id)sharedManager {
    static id sharedManager = nil;

    if (sharedManager == nil) {
        sharedManager = [[self alloc] init];
    }

    return sharedManager;
}
@end

/* 线程安全的实现 */
@implementation SomeManager

static id sharedManager = nil;

+ (void)initialize {
    if (self == [SomeManager class]) {
        sharedManager = [[self alloc] init];
    }
}

+ (id)sharedManager {
    return sharedManager;
}
@end
```
关于为什么上述代码就能实现单例模式，以及关于线程安全问题的考量，请参考他的[博客](http://eschatologist.net/blog/?p=178)。

最后介绍一个比较[现代的单例模式实现](http://lukeredpath.co.uk/blog/a-note-on-objective-c-singletons.html)。为什么说现代呢？因为这种实现利用了GCD（Grand Central Dispatch）和ARC（Automatic Reference Counting）。核心代码如下：

``` objc
+ (id)sharedInstance
{
  static dispatch_once_t pred = 0;
  __strong static id _sharedObject = nil;
  dispatch_once(&pred, ^{
    _sharedObject = [[self alloc] init]; // or some other init method
  });
  return _sharedObject;
}
```
作者还写了一个宏（[gist](https://gist.github.com/1057420)）来方便使用，大家可以阅读作者的博文[A note on Objective-C singletons](http://lukeredpath.co.uk/blog/a-note-on-objective-c-singletons.html)了解详情。

大多数情况下，Apple官方文档里的单例模式的示例代码实现已经够用了。虽然它最繁琐，但是也是本文介绍的几种单例模式中最容易理解的一个。至于其他的实现就留给读者们根据需要选择和应用了。

(全文完)