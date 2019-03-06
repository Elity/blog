---
title: 网页中使用谷歌在线字体库
tags:
  - 网页字体
  - 谷歌在线字体库
url: 1443.html
id: 1443
categories:
  - 编程随想
date: 2012-06-15 19:48:26
---

          自己的网站中使用谷歌的在线字体库的好处很多，对于喜欢用多种字体做网站的朋友可以省去好多流量，同时在线字体库提供了许多优秀的字体，这是自己一个人没法实现的。但缺点也是显而易见的，由于各种原因，谷歌在大陆时不时被墙了，会导致字体加载不正常，这时就只能使用备用字体了

比如要使用这个字体：Ubuntu Mono

官方地址：[http://www.google.com/webfonts/#QuickUsePlace:quickUse/Family:Ubuntu+Mono%7CPT+Mono](http://www.google.com/webfonts/#QuickUsePlace:quickUse/Family:Ubuntu+Mono%7CPT+Mono)

使用方法：

在网页的“head”中引入如下代码

  

然后在CSS中调用字体即可

`body{font-family:'Ubuntu Mono',sans-serif,其他备用字体;}`

更多字体参见谷歌在线字体库网址

[http://www.google.com/webfonts/](http://www.google.com/webfonts/)