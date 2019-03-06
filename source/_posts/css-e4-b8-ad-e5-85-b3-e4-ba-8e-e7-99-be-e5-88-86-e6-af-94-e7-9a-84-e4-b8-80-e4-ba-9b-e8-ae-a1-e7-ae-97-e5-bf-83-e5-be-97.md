---
title: CSS中关于百分比的一些计算心得
tags:
  - CSS
url: 2112.html
id: 2112
categories:
  - 编程随想
date: 2017-01-10 17:52:28
---

**width&height**

1.  常规元素/浮动元素设置百分比参照有明确指定的height/width且已经生效的父元素height/width值(content-box)

为什么强调已生效？因为当父元素的父元素未指定height/width时，父元素的height/width指定百分比等同于auto，或者当父元素为行内元素时，为其指定高宽也是无效的

2.  常规元素/浮动元素的父元素若为行内元素，则按照(1)中的规则寻找父级的父级做参考

3.  绝对定位的元素设置百分比参照其相对定位的祖元素有明确指定的且已经生效height/width值(padding-box)，若祖级元素无定位(默认static)设置，则参照视口height/width值计算，即100vh/100vw
4.  设置了position:fixed的元素，百分比参照视口height/width值，即100vh/100vw
5.  html元素设置的百分比参照视口的height/width值，即100vh/100vw
6.  其它情况下的height百分比设置等同于auto，width百分比设置参照视口的width值计算

  **margin/padding**

1.  常规元素/浮动元素参照父元素width (无论哪个方向margin都只参照width)计算百分比值
2.  绝对定位的元素设置百分比参照其相对定位的祖元素的width实际值计算，若祖元素无定位设置，则参照视口width值计算
3.  html元素设置的百分比参照视口的width值，即100vh/100vw
4.  另：padding计算后，如果导致相对计算的元素width被撑大，则需要重新计算