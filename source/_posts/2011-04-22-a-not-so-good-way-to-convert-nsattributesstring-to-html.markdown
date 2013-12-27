---
layout: post
title: "一个不是很好的NSAttributedString转HTML的方法"
date: 2011-04-22 13:17
comments: true
categories: RTF, NSAttributesString
---

本文中的方法存在问题。保留本文仅为存档。如果你想了解如何将`NSAttributedString`转成HTML，请阅读我后来补充的文章。  

从HTML转`NSAttributesString`相对简单，因为Cocoa已经提供了相应的方法。昨晚我花了一点点时间研究了一下从`NSAttributesString`转HTML的方法，不过依然没有找到非常理想的方法。  

搜索了一番之后，找到了一个并不是很好的方法，因为暂时实在找不到好方法，所以先贴出来，等找到更好的方法之后再说。
先上代码：
<!-- more --> 
``` objc
- (IBAction)convert:(id)sender {
    attrString = [richView attributedString];
    NSArray * exclude = [NSArray arrayWithObjects:@"doctype", @"html", @"head", @"body", @"xml", nil];
    NSDictionary *htmlAtt = [NSDictionary dictionaryWithObjectsAndKeys:
                             NSHTMLTextDocumentType, NSDocumentTypeDocumentAttribute,
                             exclude, NSExcludedElementsDocumentAttribute, nil];
    NSError *error;
    NSData *htmlData = [attrString dataFromRange:NSMakeRange(0,[attrString length]) documentAttributes:htmlAtt error:&error ];
    NSString *htmlString = [[NSString alloc] initWithData:htmlData encoding:NSUTF8StringEncoding];
    [htmlView setStringValue:htmlString];
}
```
另外，我做了个界面来试了一下这个方法的转换结果：  

我们可以看到，这个方法转换出来的HTML里包含了大量的`<font>`标签。查了一下文档，发现这个API早在10.0的时候就已经有了，因此得到这样的结果一点也不奇怪。  

后来查文档，发现有人说可以看看TextEdit源码里的实现。不过我大致浏览了一遍，发现TextEdit里已经没有了RTF转HTML的实现细节了。保存为HTML的例程也仅仅是调用了FirstResponder的`-saveDocumentAs:`方法。  

不过TextEdit输出的HTML非常的漂亮，很现代化，格式化元素都用CSS来处理了。至于是如何实现的——这就真不知道了。唉！继续研究。  

{% img center /images/posts/rtf2html.png %}

附上，介绍这个方法的原文，以及用这个方法输出的HTML和TextEdit生成的HTML的比较：  

文中的方法：

``` html
<p style="margin: 0.0px 0.0px 0.0px 0.0px"><font face="Menlo" size="3" style="font: 12.0px Menlo">- (</font><font face="Menlo" size="3" color="#b41ca4" style="font: 12.0px Menlo; color: #b41ca4">IBAction</font><font face="Menlo" size="3" style="font: 12.0px Menlo">)convert:(</font><font face="Menlo" size="3" color="#b41ca4" style="font: 12.0px Menlo; color: #b41ca4">id</font><font face="Menlo" size="3" style="font: 12.0px Menlo">)sender {</font></p>
<p style="margin: 0.0px 0.0px 0.0px 0.0px"><font face="Menlo" size="3" style="font: 12.0px Menlo"><span class="Apple-converted-space">    </span></font><font face="Menlo" size="3" color="#568187" style="font: 12.0px Menlo; color: #568187">attrString</font><font face="Menlo" size="3" style="font: 12.0px Menlo"> = [</font><font face="Menlo" size="3" color="#568187" style="font: 12.0px Menlo; color: #568187">richView</font><font face="Menlo" size="3" style="font: 12.0px Menlo"> </font><font face="Menlo" size="3" color="#401082" style="font: 12.0px Menlo; color: #401082">attributedString</font><font face="Menlo" size="3" style="font: 12.0px Menlo">];</font></p>
<p style="margin: 0.0px 0.0px 0.0px 0.0px"><font face="Menlo" size="3" style="font: 12.0px Menlo"><span class="Apple-converted-space">    </span></font><font face="Menlo" size="3" color="#7134aa" style="font: 12.0px Menlo; color: #7134aa">NSArray</font><font face="Menlo" size="3" style="font: 12.0px Menlo"> * exclude = [</font><font face="Menlo" size="3" color="#7134aa" style="font: 12.0px Menlo; color: #7134aa">NSArray</font><font face="Menlo" size="3" style="font: 12.0px Menlo"> </font><font face="Menlo" size="3" color="#401082" style="font: 12.0px Menlo; color: #401082">arrayWithObjects</font><font face="Menlo" size="3" style="font: 12.0px Menlo">:</font><font face="Menlo" size="3" color="#c72c25" style="font: 12.0px Menlo; color: #c72c25">@"doctype"</font><font face="Menlo" size="3" style="font: 12.0px Menlo">, </font><font face="Menlo" size="3" color="#c72c25" style="font: 12.0px Menlo; color: #c72c25">@"html"</font><font face="Menlo" size="3" style="font: 12.0px Menlo">, </font><font face="Menlo" size="3" color="#c72c25" style="font: 12.0px Menlo; color: #c72c25">@"head"</font><font face="Menlo" size="3" style="font: 12.0px Menlo">, </font><font face="Menlo" size="3" color="#c72c25" style="font: 12.0px Menlo; color: #c72c25">@"body"</font><font face="Menlo" size="3" style="font: 12.0px Menlo">, </font><font face="Menlo" size="3" color="#c72c25" style="font: 12.0px Menlo; color: #c72c25">@"xml"</font><font face="Menlo" size="3" style="font: 12.0px Menlo">, </font><font face="Menlo" size="3" color="#b41ca4" style="font: 12.0px Menlo; color: #b41ca4">nil</font><font face="Menlo" size="3" style="font: 12.0px Menlo">];</font></p>
<p style="margin: 0.0px 0.0px 0.0px 0.0px"><font face="Menlo" size="3" style="font: 12.0px Menlo"><span class="Apple-converted-space">    </span></font><font face="Menlo" size="3" color="#7134aa" style="font: 12.0px Menlo; color: #7134aa">NSDictionary</font><font face="Menlo" size="3" style="font: 12.0px Menlo"> *htmlAtt = [</font><font face="Menlo" size="3" color="#7134aa" style="font: 12.0px Menlo; color: #7134aa">NSDictionary</font><font face="Menlo" size="3" style="font: 12.0px Menlo"> </font><font face="Menlo" size="3" color="#401082" style="font: 12.0px Menlo; color: #401082">dictionaryWithObjectsAndKeys</font><font face="Menlo" size="3" style="font: 12.0px Menlo">:</font></p>
<p style="margin: 0.0px 0.0px 0.0px 0.0px"><font face="Menlo" size="3" style="font: 12.0px Menlo"><span class="Apple-converted-space">                             </span></font><font face="Menlo" size="3" color="#7134aa" style="font: 12.0px Menlo; color: #7134aa">NSHTMLTextDocumentType</font><font face="Menlo" size="3" style="font: 12.0px Menlo">, </font><font face="Menlo" size="3" color="#7134aa" style="font: 12.0px Menlo; color: #7134aa">NSDocumentTypeDocumentAttribute</font><font face="Menlo" size="3" style="font: 12.0px Menlo">,</font></p>
<p style="margin: 0.0px 0.0px 0.0px 0.0px"><font face="Menlo" size="3" style="font: 12.0px Menlo"><span class="Apple-converted-space">                             </span>exclude, </font><font face="Menlo" size="3" color="#7134aa" style="font: 12.0px Menlo; color: #7134aa">NSExcludedElementsDocumentAttribute</font><font face="Menlo" size="3" style="font: 12.0px Menlo">, </font><font face="Menlo" size="3" color="#b41ca4" style="font: 12.0px Menlo; color: #b41ca4">nil</font><font face="Menlo" size="3" style="font: 12.0px Menlo">];</font></p>
<p style="margin: 0.0px 0.0px 0.0px 0.0px"><font face="Menlo" size="3" style="font: 12.0px Menlo"><span class="Apple-converted-space">    </span></font><font face="Menlo" size="3" color="#7134aa" style="font: 12.0px Menlo; color: #7134aa">NSError</font><font face="Menlo" size="3" style="font: 12.0px Menlo"> *error;</font></p>
<p style="margin: 0.0px 0.0px 0.0px 0.0px"><font face="Menlo" size="3" style="font: 12.0px Menlo"><span class="Apple-converted-space">    </span></font><font face="Menlo" size="3" color="#7134aa" style="font: 12.0px Menlo; color: #7134aa">NSData</font><font face="Menlo" size="3" style="font: 12.0px Menlo"> *htmlData = [</font><font face="Menlo" size="3" color="#568187" style="font: 12.0px Menlo; color: #568187">attrString</font><font face="Menlo" size="3" style="font: 12.0px Menlo"> </font><font face="Menlo" size="3" color="#401082" style="font: 12.0px Menlo; color: #401082">dataFromRange</font><font face="Menlo" size="3" style="font: 12.0px Menlo">:</font><font face="Menlo" size="3" color="#401082" style="font: 12.0px Menlo; color: #401082">NSMakeRange</font><font face="Menlo" size="3" style="font: 12.0px Menlo">(</font><font face="Menlo" size="3" color="#3d02d9" style="font: 12.0px Menlo; color: #3d02d9">0</font><font face="Menlo" size="3" style="font: 12.0px Menlo">,[</font><font face="Menlo" size="3" color="#568187" style="font: 12.0px Menlo; color: #568187">attrString</font><font face="Menlo" size="3" style="font: 12.0px Menlo"> </font><font face="Menlo" size="3" color="#401082" style="font: 12.0px Menlo; color: #401082">length</font><font face="Menlo" size="3" style="font: 12.0px Menlo">]) </font><font face="Menlo" size="3" color="#401082" style="font: 12.0px Menlo; color: #401082">documentAttributes</font><font face="Menlo" size="3" style="font: 12.0px Menlo">:htmlAtt </font><font face="Menlo" size="3" color="#401082" style="font: 12.0px Menlo; color: #401082">error</font><font face="Menlo" size="3" style="font: 12.0px Menlo">:&error ];</font></p>
<p style="margin: 0.0px 0.0px 0.0px 0.0px"><font face="Menlo" size="3" style="font: 12.0px Menlo"><span class="Apple-converted-space">    </span></font><font face="Menlo" size="3" color="#7134aa" style="font: 12.0px Menlo; color: #7134aa">NSString</font><font face="Menlo" size="3" style="font: 12.0px Menlo"> *htmlString = [[</font><font face="Menlo" size="3" color="#7134aa" style="font: 12.0px Menlo; color: #7134aa">NSString</font><font face="Menlo" size="3" style="font: 12.0px Menlo"> </font><font face="Menlo" size="3" color="#401082" style="font: 12.0px Menlo; color: #401082">alloc</font><font face="Menlo" size="3" style="font: 12.0px Menlo">] </font><font face="Menlo" size="3" color="#401082" style="font: 12.0px Menlo; color: #401082">initWithData</font><font face="Menlo" size="3" style="font: 12.0px Menlo">:htmlData </font><font face="Menlo" size="3" color="#401082" style="font: 12.0px Menlo; color: #401082">encoding</font><font face="Menlo" size="3" style="font: 12.0px Menlo">:</font><font face="Menlo" size="3" color="#401082" style="font: 12.0px Menlo; color: #401082">NSUTF8StringEncoding</font><font face="Menlo" size="3" style="font: 12.0px Menlo">];</font></p>
<p style="margin: 0.0px 0.0px 0.0px 0.0px"><font face="Menlo" size="3" style="font: 12.0px Menlo"><span class="Apple-converted-space">    </span>[</font><font face="Menlo" size="3" color="#568187" style="font: 12.0px Menlo; color: #568187">htmlView</font><font face="Menlo" size="3" style="font: 12.0px Menlo"> </font><font face="Menlo" size="3" color="#401082" style="font: 12.0px Menlo; color: #401082">setStringValue</font><font face="Menlo" size="3" style="font: 12.0px Menlo">:htmlString];</font></p>
<p style="margin: 0.0px 0.0px 0.0px 0.0px"><font face="Menlo" size="3" style="font: 12.0px Menlo">}</font></p>
```

TextEdit:

``` html
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
  <meta http-equiv="Content-Style-Type" content="text/css">
  <title></title>
  <meta name="Generator" content="Cocoa HTML Writer">
  <meta name="CocoaVersion" content="1038.35">
  <style type="text/css">
    p.p1 {margin: 0.0px 0.0px 0.0px 0.0px; font: 12.0px Menlo}
    p.p2 {margin: 0.0px 0.0px 0.0px 0.0px; font: 12.0px Menlo; color: #568187}
    p.p3 {margin: 0.0px 0.0px 0.0px 0.0px; font: 12.0px Menlo; color: #c72c25}
    p.p4 {margin: 0.0px 0.0px 0.0px 0.0px; font: 12.0px Menlo; color: #401082}
    p.p5 {margin: 0.0px 0.0px 0.0px 0.0px; font: 12.0px Menlo; color: #7134aa}
    span.s1 {color: #b41ca4}
    span.s2 {color: #000000}
    span.s3 {color: #401082}
    span.s4 {color: #7134aa}
    span.s5 {color: #568187}
    span.s6 {color: #3d02d9}
  </style>
</head>
<body>
<p class="p1">- (<span class="s1">IBAction</span>)convert:(<span class="s1">id</span>)sender {</p>
<p class="p2"><span class="s2"><span class="Apple-converted-space">    </span></span>attrString<span class="s2"> = [</span>richView<span class="s2"> </span><span class="s3">attributedString</span><span class="s2">];</span></p>
<p class="p3"><span class="s2"><span class="Apple-converted-space">    </span></span><span class="s4">NSArray</span><span class="s2"> * exclude = [</span><span class="s4">NSArray</span><span class="s2"> </span><span class="s3">arrayWithObjects</span><span class="s2">:</span>@"doctype"<span class="s2">, </span>@"html"<span class="s2">, </span>@"head"<span class="s2">, </span>@"body"<span class="s2">, </span>@"xml"<span class="s2">, </span><span class="s1">nil</span><span class="s2">];</span></p>
<p class="p4"><span class="s2"><span class="Apple-converted-space">    </span></span><span class="s4">NSDictionary</span><span class="s2"> *htmlAtt = [</span><span class="s4">NSDictionary</span><span class="s2"> </span>dictionaryWithObjectsAndKeys<span class="s2">:</span></p>
<p class="p5"><span class="s2"><span class="Apple-converted-space">                             </span></span>NSHTMLTextDocumentType<span class="s2">, </span>NSDocumentTypeDocumentAttribute<span class="s2">,</span></p>
<p class="p1"><span class="Apple-converted-space">                             </span>exclude, <span class="s4">NSExcludedElementsDocumentAttribute</span>, <span class="s1">nil</span>];</p>
<p class="p1"><span class="Apple-converted-space">    </span><span class="s4">NSError</span> *error;</p>
<p class="p4"><span class="s2"><span class="Apple-converted-space">    </span></span><span class="s4">NSData</span><span class="s2"> *htmlData = [</span><span class="s5">attrString</span><span class="s2"> </span>dataFromRange<span class="s2">:</span>NSMakeRange<span class="s2">(</span><span class="s6">0</span><span class="s2">,[</span><span class="s5">attrString</span><span class="s2"> </span>length<span class="s2">]) </span>documentAttributes<span class="s2">:htmlAtt </span>error<span class="s2">:&error ];</span></p>
<p class="p4"><span class="s2"><span class="Apple-converted-space">    </span></span><span class="s4">NSString</span><span class="s2"> *htmlString = [[</span><span class="s4">NSString</span><span class="s2"> </span>alloc<span class="s2">] </span>initWithData<span class="s2">:htmlData </span>encoding<span class="s2">:</span>NSUTF8StringEncoding<span class="s2">];</span></p>
<p class="p1"><span class="Apple-converted-space">    </span>[<span class="s5">htmlView</span> <span class="s3">setStringValue</span>:htmlString];</p>
<p class="p1">}</p>
</body>
</html>
```

（全文完）