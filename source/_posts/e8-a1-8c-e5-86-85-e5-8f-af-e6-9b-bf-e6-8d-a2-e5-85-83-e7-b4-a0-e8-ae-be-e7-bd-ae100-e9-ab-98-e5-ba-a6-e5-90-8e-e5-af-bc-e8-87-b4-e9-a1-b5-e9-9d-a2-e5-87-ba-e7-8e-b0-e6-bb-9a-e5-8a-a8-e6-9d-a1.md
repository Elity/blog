---
title: 行内可替换元素设置100%高度后导致页面出现滚动条的原因探究及解决
tags:
  - CSS
  - 盒模型
url: 2188.html
id: 2188
comments: false
categories:
  - 编程随想
date: 2017-09-13 00:04:49
---

今天同事在项目上碰到一个问题，HTML及CSS如下，就是这样一个看似正常的页面，在chrome下居然出现了纵向滚动条：

    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8">
      <meta name="viewport" content="width=device-width">
      <title>TEST</title>
      <style>
        *{
          margin:0;
          padding:0;
        }
        html,body{
          height:100%;
        }
        img{
          height:100%;
          width:100%;
        }
      </style>
    </head>
    <body>
      <img />
    </body>
    </html>
    

诡异的滚动条哪里来的？ 一番搜索后，得到了一个看似简单，但极易被人忽略的点：img是行内元素中特殊的一种，叫行内可替换。 什么是行内可替换元素呢？可以配合着翻译看w3c对它的[定义](https://www.w3.org/TR/CSS21/conform.html#replaced-element)，简单来说就像img，iframe，select，video等等这些元素，其内容是超出CSS格式化模型范围的元素。相比于一般的行内元素，这些可替换行内元素最大的特点就是可以设置宽度和高度。 当然，以上这些其实并不重要，重要的是行内元素、inline-block元素，在一行内连续排版的时候是**不会忽略**标签之间的空白符的，很早之前看张鑫旭的[《去除inline-block元素间间距的N种方法》](http://www.zhangxinxu.com/wordpress/2012/04/inline-block-space-remove-%E5%8E%BB%E9%99%A4%E9%97%B4%E8%B7%9D/)一文，已然对这个特性有所了解。 然而，今天碰到这个滚动条的问题，一开始压根就没往这方面去找原因，毕竟若有行内空白符的存在应该是会导致页面出现横向滚动条，而非纵向。 当然，要解决这个问题，到这里已经很简单了，比如给父元素设置`font-size:0`、`line-height:0`都可解决，或者直接设置当前元素`display:block`。 然而，作为一名程序员，必须得有刨根问底搞清原理的精神，究竟是什么原因导致了纵向滚动条的出现呢？ 再次动用谷歌搜索大法，发现有人给出另外的修复方案：设置当前元素的css属性`vertical-align: bottom`; 说实话，看到这个方案，我是一脸懵B的。但几秒钟懵B完成后，我记得原来看到过关于vertical-align这个属性对齐参考线的一张图： ![vertical-align](http://ww3.sinaimg.cn/large/0060lm7Tly1fjh7m85d53j31080gzwin.jpg) 再查阅MDN关于[vertical-align](https://developer.mozilla.org/en-US/docs/Web/CSS/vertical-align)属性的说明，发现其默认值是：baseline。对照上图，很明显，baseline下面其实还有一大截，而baseline与这个img元素的底部对齐时，正是baseline底下这一大截导致了整个页面被“撑高”出现了纵向的滚动条。 其实，到了这里，我们发现vertical-align属性导致滚动条的出现并不是空白符的原因，因为如果是空白符的话，水平方向应该也会出现滚动条。而且，如果用开发者工具查看的话就会发现：就算页面出现了滚动条，而页面的实际高度依然和100vh是相等的，也就是说，虽然出现了滚动条，但是页面高度依然和原来没有滚动条时是一样的。那这又是为什么呢？ 我的理解是当对img,iframe等等这些可替换的行内元素以及其它的display:inline-block元素设置vertical-align属性时，就算该元素两边并无文字可参考，浏览器渲染引擎也会参考一个看不见摸不着的姑且称之为“空白元素”的元素去排版，而这个看不见摸不着的“空白元素”实际却不影响正常的文档流（不参与包含块的宽高计算）。至于导致最外层滚动条的出现，w3c貌似没有做明确的规定，另外我测试了下在Firefox 43.0.1版本下是没有滚动条的。 另外，想彻底领悟vertical-align属性的话 参考这篇文章 http://christopheraue.net/2014/03/05/vertical-align/ 对应的译文：https://segmentfault.com/a/1190000007663895