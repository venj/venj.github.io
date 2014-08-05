---
layout: post
title: "［更新］把Swift用作脚本语言"
date: 2014-08-01 15:23:18 +0800
comments: true
categories: [Swift, Script]
---

Swift是Apple开发的很酷的新语言。在看WWDC介绍Swift的时候，看到它有一个REPL交互界面，我当时就在想，Swift难道能像脚本语言一样用？如果可以的话，那岂不是一门非常有潜力的脚本语言了（当然，就跨平台而言，Swift完败）？性能优异，语法现代，REPL，还有Playground，简直无敌了！所以下载完Xcode 6 beta 1，我就试了一个简单的脚本：

```bash
#!/usr/bin/env xcrun swift
println("Hello Swift.")
```

如果装过命令行工具包，可以直接这么写：

```bash
#!/usr/bin/env swift
println("Hello Swift.")
```

保存为任意文件名，比如：`hello.swift`。然后在命令行执行：

```bash
chmod +x hello.swift
./hello.swift
```

执行`./hello.swift`的时候，CPU飚了一下，终端光标停顿了一下之后，打印出了`Hello Swift.`。然后我就发现和脚本的同级目录下多了一个可执行文件，名为`hello`。敢情执行这段“脚本”实际上是编译了脚本，然后再执行编译的结果啊。当时我没有深究，只想着照这样，用Swift写脚本是不行了。然后我就把这件事丢下了。

<!-- more -->

直到最近，我发现，原来可以给Swift加上一个参数，就可以不经过编译的步骤，直接执行“Swift脚本”。如下：

```bash
#!/usr/bin/env xcrun swift -i
println("Hello Swift.")
```

这样，就不会经过编译的步骤，直接执行Swift了。执行`./hello.swift`的时候，也没有了编译产生的卡顿。关于“脚本”的性能我没有深究，不过无论如何，作为脚本语言来用，应该是可以胜任的。

这样看来，以后有些简单的实用脚本可以用Swift来写了。当然，要成为一门合格的“脚本语言”，Swift还有很长的路要走（当然，很可能Swift根本不会往那个方向去），不过即便如此，Swift的狂热粉丝们会不会慢慢地用Swift来部分替代Shell，Perl，Python，Ruby这些脚本语言写的小程序呢？拭目以待开源社区给我们带来新的惊喜。

补充：

在执行Swift“脚本”之前，你可能要把Xcode 6 beta作为首选开发工具：

```bash
sudo xcode-select -s /Applications/Xcode6-Beta?.app
```

将上面的`?`替换为您实际在使用的beta版的版本号。Xcode 6正式发布后，直接用`Xcode.app`就可以了。

更新（2014-8-5）：

在刚刚发布的Xcode6 DP5中，把`xcrun swift`用在`#!`行里，不再需要加`-i`参数，读取命令行参数的时候，也无需使用`--`来分隔脚本和参数列表了。如下：

```
#!/usr/bin/env swift
// Install Xcode6 DP5 and up to run this script.
let args = Process.arguments

for a in args {
    println("Arg: \(a)")
}
```

执行`./test.swift a b c d e`，将输出如下内容：

```
Arg: ./test.swift
Arg: a
Arg: b
Arg: c
Arg: d
Arg: e
```

再次给Swift点个赞，看来Swift开发团队听到了大家想把Swift用于脚本开发的声音了。

（全文完）
