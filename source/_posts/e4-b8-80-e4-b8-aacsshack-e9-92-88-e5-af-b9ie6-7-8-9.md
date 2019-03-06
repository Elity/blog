---
title: 一个CSShack 针对IE6 7 8 9
tags:
  - csshack
url: 1691.html
id: 1691
categories:
  - 编程随想
date: 2013-03-02 13:24:11
---

记住它，一切都是浮云了  

#element{
     background-color:red;        /* 现代浏览器 */ 
     background-color:blue\\0;     /* 仅ie8 */  
     \*background-color:#dddd00;     /\* IE 7/6 */  IE6 7生效
     _background-color:#CDCDCD;     /* IE 6* /  仅IE6生效
}

**需要注意的是hack的写法和书写顺序，如果要让IE6  7  8  9同时生效且具有不同的效果时，顺序一定不能搞反了。**

**其他hack:**

**①ie7以上ie浏览器(包括ie7)及现代浏览器支持 !important 写法**

**②*html ie6及以下ie浏览器识别**

**写得很详细的两篇CSShack文章**

[****CSS Hack****](http://www.w3cfuns.com/forum.php?mod=viewthread&amp;tid=260&amp;page=1&amp;extra=#pid445)

[****CSS Hack - 精讲****](http://www.w3cfuns.com/thread-261-1-1.html)