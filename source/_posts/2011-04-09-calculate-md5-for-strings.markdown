---
layout: post
title: "计算字符串的MD5哈希"
date: 2011-04-09 13:07
comments: true
categories: MD5
---

字符串或数据的MD5哈希值是非常常用的。在Cocoa里很容易实现。因为这类方法非常好用，因此可以做一个`NSString`或`NSData`的Catagory。如下：  

**NSString:**

``` objc
// NSString+MD5.h

#import <foundation/foundation.h>
@interface NSString (NSString_MD5)
- (NSString *)md5;
@end

// NSString+MD5.m

#import "NSString+MD5.h"
#import <commoncrypto/commondigest.h>
@implementation NSString (NSString_MD5)
- (NSString *)md5 {
    const char *cStr = [self UTF8String];
    unsigned char result[16];
    CC_MD5(cStr, (unsigned int)strlen(cStr), result);
    return [NSString stringWithFormat:
            @"%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x",
            result[0], result[1], result[2], result[3],
            result[4], result[5], result[6], result[7],
            result[8], result[9], result[10], result[11],
            result[12], result[13], result[14], result[15]
            ];
}
@end
```
<!-- more --> 
**NSData:**

``` objc
// NSData+MD5.h

#import <foundation/foundation.h>
@interface NSData (MyExtensions)
- (NSString*)md5;
@end

// NSData+MD5.m

#import "NSData+MD5.h"
#import <commoncrypto/commondigest.h>
@implementation NSData (MyExtensions)
- (NSString*)md5
{
    unsigned char result[16];
    CC_MD5(self.bytes, self.length, result);
    return [NSString stringWithFormat:
        @"%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x%02x",
        result[0], result[1], result[2], result[3],
        result[4], result[5], result[6], result[7],
        result[8], result[9], result[10], result[11],
        result[12], result[13], result[14], result[15]
        ];
}
@end
```
MD5只是众多哈希算法中的一种。所以有空要仔细学习一下commoncrypto库里的其他函数。  
([via](http://stackoverflow.com/questions/1524604/md5-algorithm-in-objective-c))

（全文完）