---
layout: post
title: "在OS X Lion下编译APUE的示例代码"
date: 2011-09-08 23:27
comments: true
categories: [APUE, C, UNIX, Lion]
---

本文和Cocoa关系不大，不过UNIX开发也是OSX开发的一个重要组成部分，所以我就放在这里了。Advanced Programming in Unix Environment这本书的源码不能直接编译，网上也没有文章（除了这篇看不懂的[棒子写的文章](http://opticaller.tistory.com/78)）详细说明这本书源码的用法，这里简单的说明一下：
<!-- more --> 
1 解包`src.tar.gz`，解包出来的`apue.2e`文件夹。你可以随意移动这个文件夹的位置。

2 打开`path_to_apue_src/Make.defines.macos`，修改`WKDIR`的路径为`apue.2e`的绝对路径，如：

```
WKDIR=/Users/venj/Developer/Sources/C/APUE/apue #也就是path_to_apue_src
```

3 打开`path_to_apue_src/include/apue.h`，在第11行之后加入如下几行：

``` c
#ifdef MACOS
#define _DARWIN_C_SOURCE
#endif
```

4 打开终端，`cd`到`path_to_apue_src`目录，运行：

``` bash
make all
```

至此准备工作到此结束。

5 编译源码的方法如下：

``` bash
gcc -Ipath_to_apue_src/include path_to_apue_src/lib/libapue.a example.c -o exec_file 
```

方法也不算很难吧。 :D 理论上这个方法能在OSX 10.5以上的系统都可使用。

Happy learning!

（全文完）
