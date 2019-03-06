---
title: DZX以上版本DIY技巧
tags:
  - diy
  - DZ
url: 373.html
id: 373
categories:
  - 编程随想
date: 2011-06-09 10:20:11
---

1.[附件名前自动加网站名的方法](http://www.discuz.net/thread-1687038-1-1.html)（修改俩处）
======================================================================

**source/module/forum/forum_attachment.php**

查找下面的代码：

1.  $thread && $attachexists = TRUE;

用以下代码替换：

1.  $thread && $attachexists = TRUE;
2.  $attach\['filename'\] = '你的网站名称_'.$attach\['filename'\];

**source/function/function_attachment.php**

查找下面的代码：

1.  continue;

用下面的代码替换：

1.  continue;
2.  $attach\['filename'\] = '你的网站名称_'.$attach\['filename'\];

2. [使用等级图标替换星星等级的方法](http://www.discuz.net/thread-2146666-1-1.html)（修改俩处）
=========================================================================

首先，将您的等级图标上传到服务器（可以放在任意目录里，也可以新建一个目录存放。）我存放在rank目录中。并将等级图片由低到高命名为1.gif、2.gif…………

打开 **/source/function/function_forumlist.php**

找到： 

  
  
  
  
 

1.  function showstars($num) {

3.  global $starthreshold;

5.  $alt = 'alt="Rank: '.$num.'"';

7.  if(empty($starthreshold)) {

9.  for($i = 0; $i < $num; $i++) {

11.  echo '<img src="'.IMGDIR.'/star_level1.gif" '.$alt.' />';

13.  }

15.  } else {

17.  for($i = 3; $i > 0; $i--) {

19.  $numlevel = intval($num / pow($starthreshold, ($i - 1)));

21.  $num = ($num % pow($starthreshold, ($i - 1)));

23.  for($j = 0; $j < $numlevel; $j++) {

25.  echo '<img src="'.IMGDIR.'/star_level'.$i.'.gif" '.$alt.' />';

27.  }

29.  }

31.  }

33.  }

  
  
 

在这段代码下面添加：   
  
  
  
 

1.  function showrankstars($num) {

5.  echo "<img src=rank/$num.gif title='Rank: ".$num."'>";

9.  }

注意，上面这段代码的 echo "<img src=**rank**/$num.gif title='Rank: ".$num."'>"; 红色字体部分为您的等级图片存放的路径，请根据自己实际情况修改。  
  
  
  
3、打开** /template/default/forum/viewthread_node.htm**  
  
找到： 

1.  showstars

替换为：  
  
 

1.  showrankstars

5、用户组中设置用户组的星星数。例如您设置用户组星星数为5，则显示的就是5.gif图片。以此类推

6.后台更新缓存即可