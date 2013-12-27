---
layout: post
title: "在Cocoa程序里使用Gravatar"
date: 2011-04-09 13:17
comments: true
categories: Gravatar
---

Gravatar是一个非常流行的头像服务，可以根据用户的Email获取头像。下面一段简单的代码展示了如何在Cocoa里使用Gravatar。简单的界面测试：  

{% img center images/posts/gravatar.jpg %}
<!-- more --> 
代码如下：  
``` objc
- (IBAction)showGravatar:(id)sender {
    NSString *email_hash = [[self.emailField stringValue] md5];
    NSString *imageLink = [[NSString alloc] initWithFormat:@"http://www.gravatar.com/avatar/%@", email_hash];
    NSURL *imageURL = [[NSURL alloc] initWithString:imageLink];
    NSData *imageData = [[NSData alloc] initWithContentsOfURL:imageURL];
    NSImage *image = [[NSImage alloc] initWithData:imageData];
    [self.imageWell setImage:image];
    [image release];
    [imageData release];
    [imageURL release];
    [imageLink release];
}
```
实际的使用中，当然要使用异步请求和多线程，这里只是做一个简单的展示。另外，这里还用到了之前写的[文章](/blog/calculate-md5-for-strings/)里提到的MD5算法。  

p.s. Gravatar的大部分子域都被墙了，唯独www尚存。

（全文完）
