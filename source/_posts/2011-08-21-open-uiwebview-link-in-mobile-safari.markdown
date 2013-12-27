---
layout: post
title: "用Mobile Safari打开UIWebView里的链接"
date: 2011-08-21 0:31
comments: true
categories: [UIWebView, Mobile Safari]
---

`UIWebView`本身就是一个功能非常完整的浏览器。`UIWebView`经常被用来向用户展示打包在应用程序中的HTML帮助文档。有时，很多时候，帮助文档需要提供一些外链的链接。如果直接在`UIWebView`中打开这些链接，不仅会使显示帮助文档的`ViewController`变得臃肿，而且也没有Mobile Safari那么好的阅读体验。因此，大多数时候，在Mobile Safari中打开外链的链接是一个比较好的解决方法。

方法很简单，如下：

``` objc
- (BOOL)webView:(UIWebView *)inWeb shouldStartLoadWithRequest:(NSURLRequest *)inRequest navigationType:(UIWebViewNavigationType)inType {
    if (inType == UIWebViewNavigationTypeLinkClicked) {
        [[UIApplication sharedApplication] openURL:[inRequest URL]];
        return NO;
    }
    
    return YES;
}
```

这里用到的是`UIWebViewDelegate`的`-webView:shouldStartLoadWithRequest:navigationType:`方法。在示例代码中，我只是简单的在用户点击链接的时候调用Mobile Safari打开链接。在实际应用中，完全可以加入一些判断条件来针对不同的URL选择是否直接在`UIWebView`里打开或在Mobile Safari里打开。

（全文完）
