---
title: javascript之with的使用
tags:
  - Javascript
url: 1949.html
id: 1949
categories:
  - 编程随想
date: 2014-05-30 11:38:07
---

![](http://ww3.sinaimg.cn/large/6d1c4ea2gw1egw5cyv6dnj2074074jrf.jpg)

> 妹的，昨天都快写完了，一不小心点了个关闭，然后。。。就没有然后了
> 
> wordpress的自动保存功能咋就这么不靠谱呢

  记得还在懵懂学习JavaScript基础之时，坊间便有传言“with语句是低效率语句，若非必要，请不要使用该语句”，同时， ECMAScript 5 的strict mode下是禁止使用with语句的，所以一直以来我对with语句一直没啥好感。

  今天在知乎有个话题大概说的是“你觉得什么东西相当有B格”之类的，然后就有人贴了这段代码：

with(document)with(body)with(insertBefore(createElement("script"), firstChild))setAttribute("exparams","category=&userid=68497352&aplus&yunid=", id = "tb-beacon-aplus", src = (location > "https" ? "//s": "//a") + ".tbcdn.cn/s/aplus_v2.js")

据说是淘宝首页的，好奇心使然，果断跑去淘宝看了下，有图有真相哪：

点击图片查看大图

[![](http://ww1.sinaimg.cn/large/6d1c4ea2gw1egw4i3i2mmj21f302qjsg.jpg)](http://ww1.sinaimg.cn/large/6d1c4ea2gw1egw4i3i2mmj21f302qjsg.jpg "查看大图")

淘宝这种大项目一般是十分讲究效率的，居然会使用传说中的低效率代码？

我们试着用正常代码来达到上面的功能：

var s = document.createElement("script");
s.setAttribute("exparams","category=&userid=68497352&aplus&yunid=");
s.setAttribute("src",(location>"https"?"//s":"//a")+".tbcdn.cn/s/aplus_v2.js");
s.id="tb-beacon-aplus";
document.body.insertBefore(s,document.body.firstChild)

这是我能写出的最简单的代码了，当然，您可以尝试像淘宝那代码一样setAttribute，不过结果会让你很受伤！！！经过我的测试，他那样带3个以上的参数设置节点属性仅在with语句下有效，而且第三个及以后参数所设置的属性只能是HTML标准属性。原因我也不知道，有大牛愿意指教么？

代码压缩后，淘宝代码224字节，我写的代码264字节。

我得出的结论是：大的网站惜字节如金，特别是像淘宝这种每天流量巨大的网站，为了节省流量（别看只有几个字节，乘以个大的访问量后结果还是挺惊人的）而稍微牺牲下用户代码运行的效率是完全值得的。况且，在浏览器代码执行效率日新月异的今天，with语句效率真有那么低么？

秉承一颗探索的心（此刻内心略为激动。。），做了如下代码测试，

html代码：

<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>test</title>
</head>
<body>
    <div id="one" data="test data"></div>
</body>
</html>

with语句获取div#data值

var now = +new Date;
for(var i=0;i<1000000;i++){
   with(document)with(getElementById("one"))getAttribute("data")
}
console.log(new Date-now)

一般代码获取div#one的data值

var now = +new Date;
for(var i=0;i<1000000;i++){
   document.getElementById("one").getAttribute("data")
}
console.log(new Date-now)

获取属性值的代码均循环运行100W次，输出运行时间，测试浏览器为Chrome 35与IE 11：

申明：谁特么说我是IE黑，我和谁急！！！

.entry-content tr td{padding: 6px}

Chrome 35

数值单位：ms

第1次

第2次

第3次

第4次

第5次

第6次

第7次

第8次

第9次

第10次

平均值

10W次差值

单次差值

一般代码

1362

1358

1379

1377

1372

1411

1371

1341

1356

1339

1366.6

888.7

0.89μs

with语句

2237

2238

2313

2262

2248

2253

2240

2226

2308

2228

2255.3

IE 11

单位：ms

       

第1次

第2次

第3次

第4次

第5次

第6次

第7次

第8次

第9次

第10次

平均值

10W次差值

单次差值

正常情况

2352

2332

2321

2347

2342

2339

2365

2373

2353

2343

2346.7

861.7

0.86μs

with语句

3233

3222

3195

3200

3197

3200

3207

3210

3210

3210

3208.4

   由于其它软件运行的影响及两种代码运行的先后顺序，结果可能不是十分严谨，但个人认为还是不会影响我们得出最终的结论：with语句在不是嵌套十分复杂的情况下，相比于一般代码对执行效率的影响其实微乎其微。

   我想ECMAScript 5 的strict mode下禁用with语句的主要原因应该是with语句让个对象与方法与属性的关系变得更模糊， 不利于JavaScript向面向对象编程靠拢吧。