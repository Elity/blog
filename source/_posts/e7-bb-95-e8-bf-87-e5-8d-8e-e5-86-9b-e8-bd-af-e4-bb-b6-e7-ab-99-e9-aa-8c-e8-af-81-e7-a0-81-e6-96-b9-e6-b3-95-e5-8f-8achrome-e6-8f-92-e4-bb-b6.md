---
title: 绕过华军验证码方法 附Chrome扩展(修复下载)
tags:
  - chrome扩展
  - Chrome插件
  - Javascript
  - js
  - 华军软件
  - 验证码
url: 1433.html
id: 1433
categories:
  - 编程随想
  - 软件物语
date: 2012-06-15 00:04:34
---

现在的各类网站为了赚钱可谓出尽奇招，比如华军这样的：

![](http://pic.yupoo.com/a408115319/C2B3Dj34/sE8nP.png)

您不输入验证码就是不让您下载，从根本上解决了一些使用Chrome、FF之类浏览器的用户通过插件屏蔽广告

但突然冒出这么个东西对用户体验是很不好滴  让人看了很不爽

无聊看了下网页加载完成后的本地代码，发现各个下载点的下载地址其实在网页加载完成就已经加载到本地了，存在于JS的数组中，待用户输入正确的验证码再通过js还原各个下载地址，代码如下

![](http://pic.yupoo.com/a408115319/C2B3DRnq/tltwK.png)

然后找了下网页加载的JS文件，发现可疑文件[http://www.onlinedown.net/js/checkcode_1.2.js](http://www.onlinedown.net/js/checkcode_1.2.js)

![](http://pic.yupoo.com/a408115319/C2Bk4UyH/CMlSt.png)

关键代码：

> `var checkok =’checkok‘;showDownlist(type,checkok);`

本地执行一下即可看到效果

当然，如果每次下载东西都要特意输入代码执行的话，还不如直接输入验证码来得容易

解决方法一

本方法适合所有浏览器，把如下代码存为书签栏的书签，然后每次进入文件下载页点下书签即可

javascript:showDownlist(type,'checkok');

或把下面的链接拖拽到您浏览器的书签栏  下次进入华军的下载页面点击该书签即可

[华军验证码](http://javascript:showDownlist(type,'checkok'); "把我拖入书签栏")

解决方法二

本方法仅适合Chrome浏览器及一些Chrome内核浏览器（如360极速、枫树浏览器等）

下载下面的扩展安装即可解决问题，连点击书签都省了 安装扩展后的下载页面效果如图：

![](http://pic.yupoo.com/a408115319/C2Bk4Z7Q/kbntw.png)

既然是做成扩展了，顺便把广告框框和验证码框去掉了

[115下载](http://115.com/file/e7j7liyk)

[Dbank下载](http://dl.dbank.com/c0hrxma6de)