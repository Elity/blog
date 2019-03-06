---
title: CKEditor+CKFinder for wordpress
tags:
  - CKEditor
  - wordpress
url: 788.html
id: 788
categories:
  - 编程随想
date: 2011-10-03 21:57:42
---

[WordPress](http://www.hihaven.cn/tag/wordpress-2 "查看 WordPress 中所有日志")在安装了CKEditor For WordPress插件以后，如何启用CKFinder来上传图片、管理图片呢？

1、下载 [CKFinder](http://www.hihaven.cn/tag/ckfinder "查看 CKFinder 中所有日志") for PHP: [http://ckfinder.com/download](http://ckfinder.com/download)。  
  
2、 上传ckfinder文件夹的内容到服务器....../ckeditor-for-wordpress/ckfinder/   目录  
  
3、删除config.php,然后重命名ckfinder_config.php为config.php

你可以删除ckfinder文件下“_samples”和 “userfiles”例子与用户文件夹两个子文件夹，没有什么用处。

4、最后在左边[CKEditor](http://www.hihaven.cn/tag/ckeditor "查看 CKEditor 中所有日志")的管理菜单中选择upload options 中启用ckfinder功能即可。

更加丰富的CKEditor增强插件请参阅[ ](http://wordpress.ckeditor.com/)[http://wordpress.ckeditor.com/](http://wordpress.ckeditor.com/)