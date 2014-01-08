---
layout: post
title: "推View Controller时隐藏TabBar"
date: 2014-01-07 10:53:17 +0800
comments: true
categories: [UINavigationController, UITabbarController]
---

设想一个场景，RootViewController是一个TabBarController，里面的子Controller是一个NavigationController。NavigationController的子ViewController在执行`-pushViewController:animated:`的时候，默认情况下，这个新的ViewController将会被UITabBar覆盖。

但是有时，我们希望推入这个新的ViewController的时候，TabBarController也被推走。要做到这一点其实很简单，只需要在这个新的ViewController上设置`hidesBottomBarWhenPushed`属性为`YES`。

示例代码如下：

<!-- more -->

```objc
// 初始化ViewController的层级结构
UIViewController *vc1 = [[UIViewController alloc] initWithNibName:@"SomeViewControllerNibName" bundle:nil];
UINavigationController *nav = [[UINavigationController alloc] initWithRootViewController:vc1];
UITabBarController *tc = [[UITabBarController alloc] init];
tc.viewControllers = @[nav];

...

// 推入新的ViewController
UIViewController *vc2 = [[UIViewController alloc] initWithNibName:@"SomeOtherViewControllerNibName" bundle:nil];
vc2.hidesBottomBarWhenPushed ＝ YES; //这是关键！
[vc1.navigationController pushViewController:vc2 animated:YES];
```

就这么简单。

后记：好久没有写Blog了，2014年希望自己能勤快一点，多更新些。哪怕是一些很小的知识点，也尽量记录和分享出来，就当是给自己做备忘。

（全文完）
