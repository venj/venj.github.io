---
layout: post
title: "Cocoa的几种内置数据类型之间的互转"
date: 2011-05-28 9:42
comments: true
categories: Foundation
---

因为Cocoa的数据类型之间的互转在实际程序中应用很广泛，而我经常都会忘记，因此在此记录一下常用的几个互转。以后将会补充更多。

1 `NSData`和`NSString`之间互转：

``` objc
/* NSString to NSData */
NSString *theString = @"This is a string";
NSData *theStringData = [theString dataUsingEncoding:NSUTF8StringEncoding];
NSLog(@"%@\n%@", theString, theStringData);

/* NSData to NSString */
NSString *theStringFromData = [[NSString alloc] initWithData:theStringData encoding:NSUTF8StringEncoding];
NSLog(@"%@", theStringFromData);
[theStringFromData release];
```

这里用`NSUTF8StringEncoding`作为例子，实际可以使用更多其它的编码类型，详情可以参考文档。
<!-- more --> 
2 从某个路径(`NSString`)加载图片

``` objc
NSString *imagePath = [[NSBundle mainBundle] pathForImageResource:@"some_image"];
NSImage *theImage = [[NSImage alloc] initWithContentsOfFile:imagePath];
//[self.imageWell setImage:theImage];
[theImage release];
```

除了可以从`NSString`加载图片，也可以从`NSURL`加载。方法类似。

3 `NSData`和`NSImage`之间互转：

``` objc
/* NSImage to NSData (TIFF) */
NSData *someData = [NSData dataWithContentsOfFile:imagePath];
NSImage *someImage = [[NSImage alloc] initWithData:someData];
NSData *dataFromImage = [someImage TIFFRepresentation];
NSMutableString *homeDir = [[NSMutableString alloc] initWithString:NSHomeDirectory()];
[dataFromImage writeToFile:[homeDir stringByAppendingPathComponent:@"Desktop/image.tiff"] atomically:NO];
    
/* NSImage to NSData (Any Format, use png as example) */
//NSBitmapImageRep *bmprep = [NSBitmapImageRep imageRepWithData:dataFromImage]; // TIFF representation data
NSBitmapImageRep *bmprep = [NSBitmapImageRep imageRepWithData:someData]; // PNG data from file
NSDictionary *imageProperty = [NSDictionary dictionaryWithObjectsAndKeys:[NSNumber numberWithFloat:0.0], NSImageCompressionFactor, nil];
NSData *otherTypeImageData = [bmprep representationUsingType:NSPNGFileType properties:imageProperty];
[otherTypeImageData writeToFile:[homeDir stringByAppendingPathComponent:@"Desktop/image.png"] atomically:NO];
[homeDir release];
[someImage release];
```

`NSData`和`NSImage`之间互转实际上相当复杂。这里使用了`NSBitmapImageRep`作为中间对象来完成对各种图片类型的支持（主要包括TIFF，JPEG，GIF和PNG）。特别是`imageProperty`这个`NSDictionary`，在实际使用中需要参考文档进行赋值。

4 `NSURL`和`NSString之间互转：

``` objc
/* NSString to NSURL */
NSString *google = @"http://google.com";
NSURL *googleURL = [[NSURL alloc] initWithString:google];
NSLog(@"%@", googleURL);

/* NSURL to NSString */
NSLog(@"%@, %@", [googleURL relativeString], [googleURL absoluteString]);
[googleURL release];
```

从`NSString`到`NSURL`没什么说的；从`NSURL`到`NSString`，有两个方法，一个是绝对路径，一个是相对路径。对于本地文件的路径，这两个方法是有区别的；而对于网页等远程路径，这两个方法返回的值相同。

5 `CGImage`转`NSImage`

``` objc
/* CGImage to NSImage */
NSBitmapImageRep *bitmapRep = [[NSBitmapImageRep alloc] initWithCGImage:cgImage];
NSImage *image = [[NSImage alloc] init];
[image addRepresentation:bitmapRep];
[bitmapRep release];
[imageView setImage:image];
[image release];
```

（本文会在学习中增加更多内容。）

（全文完）