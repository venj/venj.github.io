---
layout: post
title: "让Alamofire使用自签名证书"
date: 2015-11-09 15:35:05 +0800
comments: true
categories: ['Alamofire', 'Swift', 'SSL']
---

Alamofire每次大版本号更新，都会引入大量API更新。3.x版更新用Swift 2.0的新特征更新了很多接口。网上很多关于Alamofire如何使用自定义SSL证书的文章都已经过时。经过一番搜索，最后找到了一个靠谱的解决方案。

<!-- more -->

```swift
// AppDelegate.swift
func configureAlamofireManager() {
    let manager = Manager.sharedInstance
    manager.delegate.sessionDidReceiveChallenge = { session, challenge in
        var disposition: NSURLSessionAuthChallengeDisposition = .PerformDefaultHandling
        var credential: NSURLCredential?

        if challenge.protectionSpace.authenticationMethod == NSURLAuthenticationMethodServerTrust {
            disposition = NSURLSessionAuthChallengeDisposition.UseCredential
            credential = NSURLCredential(forTrust: challenge.protectionSpace.serverTrust!)
        } else {
            if challenge.previousFailureCount > 0 {
                disposition = .CancelAuthenticationChallenge
            } else {
                credential = manager.session.configuration.URLCredentialStorage?.defaultCredentialForProtectionSpace(challenge.protectionSpace)

                if credential != nil {
                    disposition = .UseCredential
                }
            }
        }
        return (disposition, credential)
    }
}
```

在AppDelegate.swift中创建上面这个方法，在`application(_:, didFinishLaunchingWithOptions:) -> Bool`方法中调用一次这个方法，这样，后续的使用Alamofire默认的`request`方法发起的请求就可以使用自定义SSL证书了。

如果不要全局跳过自定义SSL证书检查，你可以在需要跳过自定义SSL请求的时候，单独创建一个`Alamofire.Manager`对象，然后再调用上述方法中的`delegate.sessionDidReceiveChallenge`，然后用`manager.request`创建`request`进行Web请求。

参考：[StackOverflow](http://stackoverflow.com/questions/30671029/alamofire-ssl-failure)

（全文完）
