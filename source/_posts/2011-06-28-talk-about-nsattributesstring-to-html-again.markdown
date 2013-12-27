---
layout: post
title: "再谈NSAttributedString转HTML"
date: 2011-06-28 22:59
comments: true
categories: [NSAttributesString, RTF]
---

我之前曾经写过一篇[关于NSAttributedString转HTML的文章](/blog/a-not-so-good-way-to-convert-nsattributesstring-to-html/)，因为没有得到理想的结果，而TextEdit的另存为HTML却非常漂亮，所以对此我一直耿耿于怀。今天又研究了一下TextEdit将文档另存为HTML的方法，总算能把这个问题画上一个圆满的句号了。

在来回翻阅了TextEdit的源码后，我发现在`Document.m`里的`- (id)fileWrapperOfType:(NSString *)typeName error:(NSError **)outError`方法，就是苹果官方将`NSAttributedString`转HTML的方法（这个方法当然也包含了将富文本转成其他格式的方法，在此不详述了）。仔细阅读了这个方法之后，我发现原来苹果也是用的我在之前那篇文章中用到的那个方法：`- (NSData *)dataFromRange:(NSRange)range documentAttributes:(NSDictionary *)dict error:(NSError **)error`转的——我没把重点弄错。不错我的错误是错在调用的时候，设置的参数有问题。
下面贴上源码，在注释了进行解释：
<!-- more --> 
``` objc
- (IBAction)convert:(id)sender {
    attrString = [richView attributedString];
    NSMutableArray *exclude = [NSMutableArray array];
    // 如果要使用XHTML文档，可以注释掉下面这行，否则会使用HTML 4.01。
    [exclude addObject:@"XML"];
    // 如果要使用HTML Transitional，注释掉下面这行。否则就用HTML Strict。
    // 不过HTML Transitional支持font标签，而这显然不是我们想要的。
    [exclude addObjectsFromArray:[NSArray arrayWithObjects:@"APPLET", @"BASEFONT", @"CENTER", @"DIR", @"FONT", @"ISINDEX", @"MENU", @"S", @"STRIKE", @"U", nil]];
    // 如果要将CSS放入HTML的head标签中，可以注释掉下面这样：
    [exclude addObject:@"STYLE"];
    // 如果要完全不用CSS，同时注释掉上面这样和下面这行。
    //[exclude addObject:@"SPAN"];
    // 如果要保留空格字符，注释掉下面几行——因为HTML会把任意长的空格合成一个空格的。
    //[exclude addObject:@"Apple-converted-space"];
    //[exclude addObject:@"Apple-converted-tab"];
    //[exclude addObject:@"Apple-interchange-newline"];
    // 如果不要完整的HTML结构，则取消注释下面一行。
    //[exclude addObjectsFromArray:[NSArray arrayWithObjects:@"doctype", @"html", @"head", @"body", nil]];
    
    NSDictionary *htmlAtt = [NSDictionary dictionaryWithObjectsAndKeys:
                             NSHTMLTextDocumentType, NSDocumentTypeDocumentAttribute,
                             exclude, NSExcludedElementsDocumentAttribute, nil];
    NSError *error;
    NSData *htmlData = [attrString dataFromRange:NSMakeRange(0,[attrString length]) documentAttributes:htmlAtt error:&error ];
    NSString *htmlString = [[NSString alloc] initWithData:htmlData encoding:NSUTF8StringEncoding];
    [htmlView setStringValue:htmlString];
    NSLog(@"%@",htmlString);
}
```

下面是测试的结果（未包含完整的HTML结构）：

``` html
<p style="margin: 0.0px 0.0px 0.0px 0.0px; font: 12.0px Menlo">- (<span style="color: #b41ca4">void</span>)applicationDidFinishLaunching:(<span style="color: #7134aa">NSNotification</span> *)aNotification</p>
<p style="margin: 0.0px 0.0px 0.0px 0.0px; font: 12.0px Menlo">{</p>
<p style="margin: 0.0px 0.0px 0.0px 0.0px; font: 12.0px Menlo; color: #1a8600"><span style="color: #000000"><span class="Apple-converted-space">    </span></span>// Insert code here to initialize your application</p>
<p style="margin: 0.0px 0.0px 0.0px 0.0px; font: 12.0px Menlo">}</p>
```

虽然有很多冗余的CSS，但是总算告别满屏的`<font>`标签了，而且知道了如何对生成的HTML进行控制，应该说，比较完美的解决了富文本转HTML的问题了——这毕竟是苹果官方的方法啊。

至于如何对生成的HTML进行进一步优化，那就是另一个新问题了，所以留给以后解决吧。

（全文完）