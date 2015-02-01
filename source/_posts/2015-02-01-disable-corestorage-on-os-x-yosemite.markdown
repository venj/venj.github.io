---
layout: post
title: "禁用OS X Yosemite上的Core Storage"
date: 2015-02-01 14:04:30 +0800
comments: true
categories: ["OS X Yosemite", "Core Storage", "Recovery分区"]
---

不管你发现没有，OS X Yosemite已经悄悄的把你的磁盘转成Core Storage了。做过Fusion Drive的同学一定知道Core Storage是实现Fusion Drive的核心技术。不过对于只有一个磁盘的MacBook来说，我有点不太理解苹果强制启用CoreStorage的理由。

** 为什么要关闭Core Storage **

当然，如果你在用Fusion Drive，那么Core Storage是你的好朋友。<span style="color:red;">如果你正在使用Fusion Drive，请无视本文！！！</span>

不过对于单磁盘的Mac来说，Core Storage有如下问题：

1. 按住Option开机，Recovery分区不见了；（当然，你可以按住Command + R启动，进Recovery。）
2. 无法随意调整Mac的分区了；
3. 磁盘实用工具尚未对Core Storage提供完整支持；
4. Windows下无法挂载Core Storage分区，即使你装过BootCamp驱动；
5. 磁盘性能下降？（这个可能是个例。我升级OS X 10.10.2之后，明显感到系统卡顿，关闭Core Storage之后恢复正常。）

<!-- more -->

** 如何知道是否已经启用了Core Storage **

首先，确定你的系统是OS X Yosemite 10.10.x。然后，打开“磁盘实用工具”，如果你看到启动磁盘的显示为2个Macintosh HD（如下图），那么基本上你已经在用Core Storage了。

{% img center /images/posts/cs_du.png %}

打开“终端”，执行如下命令：

```bash
diskutil cs list
```

如果你看到一个逻辑磁盘阵列的树形结构，而不是：

```
No CoreStorage logical volume groups found
```

那么，恭喜你，你正在使用Core Storage。耶。

** 你需要关闭Core Storage吗？ **

如果你不在意上面提到的1-4，并且，你也没有遇到磁盘性能下降的问题，那么你大可不必去禁用Core Storage。如果你仔细问过自己之后，还是决定要关掉Core Storage，那么请继续往下读。

** 如何关闭Core Storage **

首先，请备份好您重要的数据。如果你有Time Machine备份的话，那么请放心的继续进行下面的操作。

我一再强调备份数据的重要性，倒并不是说这个操作有多大的风险；而是提醒你数据安全的重要性。如果一切就绪，那么我们开始把。

打开终端，执行如下命令：

```bash
sudo diskutil cs revert /
```

输入密码，稍等片刻即可完成。命令执行完毕后提示你尽快重新启动系统。我的建议是立刻重启。重启完成后，检查一下Core Storage是否已经成功关闭。如果你在磁盘实用工具里，看到如下的状况，那么您已经成功了。副产品是，按住Option启动时，Recovery分区也回来了。

{% img center /images/posts/cs_du2.png %}


** 总结 **

如果你搜索过这个话题，并且搜索到各种复杂的关闭Core Storage的方案，请千万不要尝试。关闭Core Storage只需1条命令，就这么简单！如果你执行失败，请进入恢复模式后尝试一次。最后祝你操作顺利。

（全文完）