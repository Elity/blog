---
title: wordpress评论框中添加提示文字或圖片
tags:
  - wordpress
url: 660.html
id: 660
categories:
  - 编程随想
date: 2011-09-26 00:58:50
---

添加文字

我想很多朋友应该见过，在一些WordPress博客的评论狂中会有一段提示问题，就像一些站点的搜索框中的提示文字那样，当鼠标点击时会自动消失，就像本站的评论框这样，来看一下如何实现的吧！

方法很简单，在主题目录的comments.php中找到这段代码：

 \[ccein_html\]\[/ccein_html\]

將以上代码修改为

\[ccein_html\]友情提示：请勿发表色情、违法、商业广告等信息！\[/ccein_html\]

如果你不想让评论框中的文字是灰色的，可以把这句去掉：

> \[ccein\_html\]style="color:gray"\[/ccein\_html\]

这样，当你鼠标点击评论框时提示文字就自动消失了！

添加圖片

是否觉得你的WordPress评论框太单调呢？前面有说到给WordPress评论框添加提示文字，这里再来看看给WordPress评论框添加背景图片吧！

效果图：

[![](http://ccc5.cc/wp-content/uploads/2011/09/WordPress_thumb1-300x125.jpg "WordPress_thumb1")](http://ccc5.cc/wp-content/uploads/2011/09/WordPress_thumb1.jpg)

方法：

找到主题目录的comment.php文件，一般评论框都是用textarea代码来控制，那么就看textarea文本区域对应的CSS样式表ID是多少，一般为comment，这样我们就到主题样式表style.css里找到#comment代码，类似于这样的：

> #comment {width:645px;}

我们将其修以下代码

> #comment {width:645px;background-image:url(img/comment-bg.gif);background-repeat:no-repeat;background-position:right bottom;}

自己把代码中的图片修改一下即可，

相关解释：

width:是定义评论框的宽度；

background-image:url(图片地址)，这里的图片可以绝对地址，也可以是相对地址，相对地址是相对style.css文件的，我就是用的相对地址。

background-repeat:no-repeat; 定义图片是不是重复；

background-position:right bottom; 定义图片对齐方式，这里是向右、底对齐。