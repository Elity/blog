---
title: Discuz X1.5 论坛默认宽屏，带窄屏切换功能设置
tags:
  - DZX1.5
  - 技巧
  - 论坛
url: 234.html
id: 234
categories:
  - 编程随想
date: 2011-05-02 07:49:32
---

  
  
官方首页默认为窄版，但是很多人还是比较眷顾以往的宽版，目前后台无法设置，所以可以设置如下，即可以默认宽版，又可以切换到窄版；  
  
演示网站：http://www.ccc5.cc  
  
打开header.htm文件（文件地址：template/default/common/header.htm），看到两处是设置款窄屏显示的，一处是判断是否显示调用宽屏的js和css代码：

1.  <!--{if $\_G\['basescript'\] == 'forum' && !empty($\_G\['cookie'\]\['widthauto'\]) && empty($_G\['disabledwidthauto'\])}-->
2.  <link rel="stylesheet" id="css\_widthauto" type="text/css" href="data/cache/style\_{STYLEID}_widthauto.css?{VERHASH}" />
3.  <script type="text/javascript">HTMLNODE.className += ' widthauto'</script>
4.  <!--{/if}-->

_复制代码_

 默认状态是widthauto不为空及disablewidthauto为空是调用宽屏，那么去掉这个判断就是直接调用宽屏了，即：

1.  <link rel="stylesheet" id="css\_widthauto" type="text/css" href="data/cache/style\_{STYLEID}_widthauto.css?{VERHASH}" />
2.  <script type="text/javascript">HTMLNODE.className += ' widthauto'</script>

_复制代码_

替换之后更新缓存就可以宽屏显示了。  
但是还有个问题，那就是当前状态时宽屏，切换处还是切换到宽屏，我们要的是切换到窄屏，所以就要改第二处:

  
  

1.  <ul class="wslct">
2.  <li><a href="javascript:;" onClick="widthauto(this)"><!--{if empty($\_G\['cookie'\]\['widthauto'\])}-->{lang switch\_wide}<!--{else}-->{lang switch_narrow}<!--{/if}--></a></li>
3.  </ul>

_复制代码_

当前是切换到宽屏，则将{lang switch\_wide}与{lang switch\_narrow}替换一下位置即可。  
最后不要忘记更新缓存哦~  
至此，宽频修改成了！