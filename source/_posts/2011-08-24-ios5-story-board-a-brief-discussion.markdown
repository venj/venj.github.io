---
layout: post
title: "浅谈iOS 5的StoryBoard"
date: 2011-08-24 0:34
comments: true
categories: [Cocoa Touch, iOS 5, MVC, NIB, StoryBoard, XIB]
---

StoryBoard是iOS 5的新特征，旨在代替历史悠久的NIB/XIB（其实**StoryBoard还是基于NIB/XIB的**，不过开发人员已经无需直接跟NIB打交道了）。目前关于StoryBoard的文档并不多，苹果的iOS 5的开发者文档里也仅有不多的介绍。所以，本文只是简单的谈谈本人对StoryBoard的一些粗浅的理解。（StoryBoard有时也叫做StoryBoarding，我不太注意这种细节，所以两个词经常会混用，如果你英语可以的话，能体会到两者的细微差别）

{% img center /images/posts/storyboard-1.png %}
<!-- more --> 
StoryBoarding机制比之NIB/XIB的的优势何在呢？个人认为，StoryBoard有以下几个优点：

> 1. 能够减少很多跟View相关的代码；
> 2. 能够使View和Controller进一步解耦；
> 3. 能够优化程序的“页面流”，使程序的结构更清楚 ；

要理解这些优点，我们先要对NIB有一个基本的认识。通常，NIB是和ViewController相关联的，很多ViewController都有对应的NIB文件。NIB文件的作用是描述用户界面以及初始化对象和界面元素对象。其实开发者在NIB里描述的界面和初始化的对象都能够在代码中实现；之所以用Interface Builder来绘制界面，是为了减少那些设置界面属性的无聊和重复的代码，让开发人员能够集中精力做程序的功能。

而StoryBoard的出现，则是进一步加强了这方面的功能；NIB文件是没有办法描述从一个ViewController到另一个ViewController的过渡的。这种过渡只能靠手写代码来实现。相信很多人都会经常用到 `-presentModalViewController:animated:`以及`-pushViewController:animated:`这两个方法。这种代码在Storyboarding里将成为历史；取而代之的是Segue。Segue定义了从一个ViewController到另一个ViewController的过渡。在Storyboard里，我们只需要像连接界面对象和Action Method那样把ViewController之间用Segue连接起来就可以了，不再需要手写代码了。即便你像自定义Segue，你也只需写Segue的实现，而无需编写调用的代码，StoryBoard会帮你调用的。这就是上面所说的第一个优点。

{% img center /images/posts/storyboard-2.png %}

要用好Storyboarding机制，那么必须严格遵守MVC原则。要让View和Controller充分解耦；并且不同的Controller之间也要充分解耦。否则，程序的业务逻辑就会乱成一团，很难理解，维护和除虫（Debug）。

举个例子来说：在过去，特别是初学Cocoa Touch开发的时候，很多人都喜欢直接把`AppDelegate`当ViewController用，直接在AppDelegate和`MainMenu.xib`之间交互。应该说，**这是一个非常不好的习惯**。`AppDelegate`的作用很简单，就是处理`UIApplication`的回调，而不应该负责用户界面的处理。很多iOS教程为了省事，都直接把`AppDelegate`当ViewController用，甚至直接举例在`UIWindow`上绘制界面。虽然，作为教程这么做很简单明了，因为`UIWindow`也是`UIView`的子类，但是这却不是一种优良的实践。因为**由ViewController来负责处理View才是正确的做法**。

近一段时间，苹果的项目模版经常发生改变，特别是自从Xcode 4发布之后，程序模版（如，View Based Application）开始鼓励使用`UIWindow`的`rootViewController`属性来指定第一屏的ViewController，以保证`AppDelegate`专注于它应该做的事情。而引入StoryBoard之后，`AppDelegate`已经不管ViewController的事情了 ；第一屏所使用的ViewController（也就是`rootViewController`）可以在StoryBoard中设置。这样，程序的入口点就能从StoryBoard的“设计图”上一目了然了。这是第二个优点。

{% img center /images/posts/storyboard-3.png %}

至于第三个优点，就是StoryBoard的“设计图”了。StoryBoard能够包含一个程序所有的ViewController以及它们之间的连接。因此，StoryBoard甚至可以作为程序的“设计图”来用了。理想情况下，在程序开发接近尾声的时候，我们只需对比StoryBoard的“流程”和最初程序的设计“流程”，就知道程序有没有“走样”了。

{% img center /images/posts/storyboard-4.png %}

说完了优点，我们来看看从NIB/XIB到StoryBoard的迁移，我们需要有哪些理解和实践上的改变呢？

> * 首先，自然是（在做程序开发的时候）ViewController不再需要NIB/XIB了（虽然在后台还是用的NIB）。以前在NIB/XIB上做的连接Outlet和Action的操作都可以在StoryBoard上完成了；
> * 第二，孤儿View（独立于ViewController的View）是不能出现在StoryBoard里的，View必须通过ViewController来管理（StoryBoard更像是Controller对象的容器，而不是View对象的容器，NIB/XIB可以作为View对象的容器）；
> * 第三，ViewController之间的过渡代码已经是历史了，用StoryBoard可以直接可视化地连接不同的ViewController；
> * 第四，UIWindow对象的作用被进一步淡化，甚至可以这么说：其实很多程序根本无需用到UIWindow对象。`AppDelegate`也不再被鼓励（也不能）用来做ViewController－－你甚至无法在Interface Builder的StoryBoard图上找到`AppDelegate`对象－－因为它本来就不应该用来处理界面（View）的。
> * 最后，写优质的代码，严格遵守MVC设计模式，这样不仅能够让你用好StoryBoard，也能帮助你理解StoryBoard的原理。

StoryBoard是非常好的鼓励MVC和代码解耦的手段，能够让开发人员写出更加容易维护的代码。不过对于初学者来说，确实是个对理解力的小挑战。不过作为初学者也不用担心，一旦突破了理解障碍，你就会发现StoryBoard也非常好用－－就像最初理解NIB/XIB时，Outlet和Action“拉线”来“拉线”去，看起来也很神奇；理解之后，发现原来“拉线”神马的也没那么神秘。

好了，絮絮叨叨的啰嗦了这么多无聊的文字，相信你也看累了。如果你依然对我写的东西不知所云的话，你可以稍稍研究一下Xcode 4.2的几个内建模版，然后和使用XIB的模版对比一下，看看苹果是怎么用StoryBoard的，能够很好的帮助你理解Storyboarding机制。当然，千万不要忘记亲自动手用一用StoryBoard！

Happy Coding!

**【更新】**

在看了WWDC 2011 的session video：UIViewController Containment这个视频之后，我感觉，正确的处理`UIViewController`之间的层级关系对于Storyboard来说非常重要，两者能够相得益彰。而`window.rootViewController`的使用，虽然也是鼓励用户不要把`AppDelegate`当ViewController用，更重要的是鼓励用户使用正确的`UIViewController`层级关系。

（全文完）
