---
title: javascript利用window.name实现跨域传输数据
tags:
  - Javascript
  - 跨域
url: 1874.html
id: 1874
categories:
  - 编程随想
date: 2013-09-15 14:44:18
---

好吧。。我OUT了，直到前几天才知道window.name居然有这神奇功效

例如：有a.com和b.com两个域，a.com/res.html想接收b.com/send.html发来的数据

可以这么实现，

①先在b.com/send.html下利用javascript设置window.name 为需要传输的数据

②然后在a.com/res.html下插入iframe,其src指向b.com/send.html

③理论上此时a.com/res.html下的window\["iframe"\].contentWindow.name应该就是b.com/send.html下设置的window.name了

但这只是理论，由于浏览器的跨域限制，我们在此时并不能访问window\["iframe"\].contentWindow.name

④既然不能访问， 那么我们便将iframe的src指向a.com下的一个空白页面（如proxy.html）,此时再访问window\["iframe"\].contentWindow.name，发现b.com 传输过来的 window.name在src改变后仍然神奇般地保存了下来。。

当然前提是proxy.html没有设置window.name

大致就是这样了  ，下面附上网上流传很广的代码

**b.com/send.html**

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
<head>
    <meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
    <title>Document</title>
</head>
<body>
    <script type="text/javascript">
    window.name = '需要跨域的数据'; 
</script>
</body>
</html>

**a.com/res.html**

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
<head>
    <meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
    <title>test window.name跨域</title>
</head>
<body>
    <script>
    var state = 0, 
    iframe = document.createElement('iframe'),
    loadfn = function() {
        if (state === 1) {
            alert("state=1");
            var data = iframe.contentWindow.name;    // 读取数据
            alert(data);    //弹出 '需要跨域的数据'
        } else if (state === 0) {
            alert("state=0");
            state = 1;
            iframe.contentWindow.location = "http://a.com/proxy.html";    // 把src设为本地域
        }  
    };
    iframe.src = 'http://b.com/send.html';
    iframe.onload  = loadfn;
    document.body.appendChild(iframe);
    </script>
</body>
</html>

proxy.html设为空白页面就好了

============== 纠错分割线 2013.09.26====================

今天翻看《javascript高级程序设计 第三版》268页， 其中提到

IE7 及更早版本的IE浏览器中

> 不能设置动态创建的<iframe>元素的name特性

那么我们上面的代码在兼容性上是有问题的。

。

。

。

写着写着发现我们上面并不是设置的iframe的name,好吧  我神经了

不过好在对那句话印象深刻了