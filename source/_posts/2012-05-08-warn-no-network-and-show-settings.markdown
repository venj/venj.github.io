---
layout: post
title: "检测iOS的网络可用性并打开网络设置"
date: 2012-05-08 10:39
comments: true
categories: ["iOS", "Info Plist", "URL Scheme"]
---

今天接到个需求，要求程序能够检测网络可用性，并在没有网络可用的时候能够弹出对话框，并允许用户点击按钮打开网络设置。

这个问题，我首先想到的就是用一个方法检测网络可用性，然后用`UIApplication`的`openURL`方法打开某个特殊URL，就可以进入设置了。于是，我迅速地建了个测试项目，写了个简单的实现，如下：
<!-- more -->
``` objc
// 注意：这个方法仅对iOS 5.0.x有效
+ (BOOL) isNetworkAvailable { //Via Stackoverflow
    NSURL *url = [NSURL URLWithString:@"http://www.baidu.com"];
    NSURLRequest *request = [NSMutableURLRequest requestWithURL:url];
    NSHTTPURLResponse *response = nil;
    [NSURLConnection sendSynchronousRequest:request
                          returningResponse:&response error:NULL];
    return (response != nil);
}

- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    if (![[self class] isNetworkAvailable]) {
        [[[UIAlertView alloc] initWithTitle:@"No network" message:@"No networkNetwork unavailable!!!" delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"Settings", nil] show];
    }
}

- (void)alertView:(UIAlertView *)alertView didDismissWithButtonIndex:(NSInteger)buttonIndex {
    if (buttonIndex != 0) {
        [[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"prefs:root=General&path=Network"]];
    }
}
```

编译运行之后，网络不可用倒是顺利提示了，但是我很杯具的发现，点了提示框里的“Settings”按钮之后完全没有反应。于是STFW，然后悲催的发现“特殊URL”在iOS 5.1里已经失效。多方搜索基于`openURL`的方法无果。

正当我快放弃的时候，我发现高德地图在iOS 5.1下居然有类似的功能！（难道高德地图用了Private API，而且没有被苹果发现？我偷偷的这么想。）我对着高德地图又研究了好久，发现高德地图居然能够判断有无蜂窝网络和飞行模式！（嗯，肯定是Private API无误了！【误！】）

然后突然灵光一闪，是不是`Info.plist`在作祟呢？因为`Info.plist`可以检测有无摄像头啥的，检测网络应该也没问题吧。于是，我解包了高德地图，打开`Info.plist`一看，果然有一条名为`SBUsesNetwork`的`Boolean`类型的记录。

然后我就注释了自己的代码，在`Info.plist`里增加了一条：

```xml
<key>SBUsesNetwork</key>
<true/>
```

编译运行，终于出现了高德地图一样的提示对话框。按下“设置”按钮，顺利转跳到了“设置”程序里，问题解决。

经过这次乌龙，我只有再次感叹，Cocoa (Touch)深似海，而我的脚才刚踩上沙滩。

顺道：我真的用软件检测了高德地图是否用了Private API。答案当然是否定的。【我这个白痴】

(全文完)