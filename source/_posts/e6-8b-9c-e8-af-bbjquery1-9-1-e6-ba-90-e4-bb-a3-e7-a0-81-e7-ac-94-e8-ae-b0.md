---
title: 拜读jQuery1.9.1源代码笔记
tags:
  - JQuery
  - 笔记
url: 1730.html
id: 1730
categories:
  - 随手写写
date: 2013-04-05 19:14:26
---

![](http://ww4.sinaimg.cn/large/a74ecc4cjw1e3ey9q5dgcj.jpg)

jQuery由世界诸多javascript高手共同维护，对于有那么一点js基础的菜鸟来说，能静下心来阅读并理解jQuery源代码，对于自己的js水平的提高是很有益处的。

jQuery1.9.1源代码地址[http://code.jquery.com/jquery-1.9.1.js](http://code.jquery.com/jquery-1.9.1.js)

以下记录下我从今天开始阅读代码的心得、疑问、网友的解答以及自己的一些测试

①匿名函数中传入undefined的疑问

(function(window,undefined){
core_strundefined = typeof undefined
//other jQuery code
})(window)

传入window可以将window作为局部变量，每次调用效率会更高，但undefined没必要吧，况且里面还判断undefined的类型，显然不是为了执行的效率问题

网上找了些资料，有网友说undefined可被重写，老实说，头一次听说，立马在浏览器控制台测试

var undefined = 123;
alert(undefined);

居然没报错，说明undefined不是关键字，可被声明为自定义变量，但上面的代码弹出结果依然是undefined，说明没被重写成功。

继续测试如下代码

(function(){
    var undefined = 123;
    alert(undefined);
})()

果然，里面的undefined变成了123；

jQuery为了防止外层被重写了的undefined污染自己内部的undefined，对于匿名函数定义的形参undefined不予传入值（只为第一个参数传入了window），那么作为jQuery局部变量的undefined当然就是undefined了；

而下面就是内部undefined被外围的污染了

(function(){
　　var undefined = 123;
　　(function(){
　　   alert(undefined);
   })()
})()

网友提供了另一种方法获取undefined

var undefined = void(0);

不过没jQuery来得那么简单

另外：在JS中和undefined一样，可以被重写的关键字还有许多  见下图（来自javascript权威指南）

![](http://ww1.sinaimg.cn/large/a74ecc4cjw1e482jzjrhpj20kx047aau.jpg)

写代码的时候应当注意不要去重写这些关键字，如果没有如jQuery那样正确去处理，则造成的错误可能很难发现