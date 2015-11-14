---
layout: post
title: "在Xcode Playground中使用Framework时的一个小坑"
date: 2015-11-14 21:02:47 +0800
comments: true
categories: ["Playground", "Framework", "Xcode"]
---

在项目开发的过程中，Playground是一个很好的测试开发思路的地方。独立的Playground只能使用平台自带的框架，但是如果在项目中添加了Playground，它就可以使用项目包含的所有框架－－无论是第三方框架还是自己开发的框架。

但是今天一不小心碰到了一个坑。最初我在一台电脑上用的好好的Playground，在另一台电脑上，签出同样的代码之后，Xcode的控制台居然一直报错，提示找不到方法。

{% img center /images/posts/framework_playground_0.png %}

经过一番检查之后发现两台电脑上的代码并没有任何差异，gitignore也没有ignore错任何文件。郁闷一番之后，只好请教搜索引擎了。搜索了关于如何在Playground中引入框架的文章之后，其中的一句话点醒了我：Playground运行的是64位代码。果然，看了两边的差别，就是选择的iOS模拟器不同：一边正常的是iPhone 6S Plus；另一边不正常的是iPhone 5。仔细一想，iPhone 5好像不是64位的。于是，把模拟器选择为iPhone 5S之后，一切正常了。

{% img center /images/posts/framework_playground_1.png %}

至此，问题解决。虽然只是个小坑，但是考虑到问题的隐蔽性，所以我还是决定简单写几句记录下来，给自己备忘，也希望能通过搜索引擎帮助遇到同样问题的人。

（全文完）
