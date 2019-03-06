---
title: 让IE9以下版本(ie6/7/8)认识html5元素
tags:
  - html5
  - Javascript
url: 1853.html
id: 1853
categories:
  - 编程随想
date: 2013-08-12 22:26:05
---

html5现在十分流行，作为前端爱好者，怎能不跟随潮流呢。。

在代码中插入html5元素后问题出现了，那些守旧用户仍在使用IE6、7、8，造成了一些麻烦：

每个浏览器都有一份清单列举自己所支持的HTML元素。不在清单上的元素都将被视为未知元素。浏览器不会给未知元素设定任何样式（不同浏览器对元素会有不同的默认样式）。在IE9之前的版本中，也不能对未知元素设定样式。未知元素的DOM也显示不正确，IE会在DOM中插入一个没有子元素的空节点。所有你原本认为将会成为这个未知元素的子元素的元素会成为其兄弟节点。

解决办法就是：

在使用里如article标签之前先用js创建一个虚假的article元素，IE就会识别这个元素了，而支持用css设定样式。这个虚假元素甚至都不需要插入到DOM中

如代码：

< !DOCTYPE HTML>
<html lang="en">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>unknown elements</title>
<style>
article{display:block;border:1px solid red}
</style>
</meta></head>
<body>
<article>
<h1>welcome to feimos's blog</h1>
<p>This is your <span>first time to visit this webSite.</span></p>
</article>
</body>
</html>

IE6不会识别article，所以也不会有红色边框。

但是如果我们在head中加入一句js，情况立马就不一样了。

代码如下：

<script type="text/javascript">
document.createElement("article");
</script>

此时IE6即可识别article了；

所以我们只需针对低版本IE浏览器添加如下JS代码，给所有的html5元素重新创建一份副本即可：

代码如下：

<!--\[if lt IE 9\]>
<script type="text/javascript">
var e=("abbr,article,aside,audio,canvas,datalist,details,"+
"figure,footer,header,hgroup,mark,menu,meter,nav,output,"+
"progress,section,time,video").split(',');
for(var i=0;i<e .length;i++){
document.createElement(e\[i\]);
}
</script>
< !\[endif\]-->

现在已经有完善的该类代码项目  [HTML5 Shiv](http://html5shim.googlecode.com/svn/trunk/html5.js)

我们只需在自己的网站中添加进如下代码即可

<!--\[if lt IE 9\]>
<script src="//html5shim.googlecode.com/svn/trunk/html5.js"></script>
< !\[endif\]-->

注：这段脚本需要放在页面起始的部分，最好是head中，不要放在底部。这样IE在解析页面标签之前就会先运行这段代码。