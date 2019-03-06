---
title: WordPress防盗链方法
tags:
  - 盗链
url: 905.html
id: 905
categories:
  - 编程随想
date: 2011-10-15 00:14:37
---

一篇精彩的原创博文,可能很快会被转载无数次.有些转载者图方便直接复制粘贴到自己的网站或博客,如果文章中有图片,也会被一起转载,造成的流量损失会很惊人.因此喜欢在日志中加入本地上传图片的童鞋,保护自己的图片不被盗链还是非常重要的.这篇简易教程可以让你轻松实现图片防盗链.  
  
  
  
安装完成Wordpress程序,一般会在WP程序目录创建一个名称为.htaccess的文件,我们就是要用到这个.htaccess文件,如果没有可以自己创建一个.

用FTP软件将.htaccess文件下载到本地电脑中,用文本编辑软件打开这个文件,里面默认内容为:

1.  # BEGIN WordPress

3.  RewriteEngine On
4.  RewriteBase /
5.  RewriteRule ^index\\.php$ - \[L\]
6.  RewriteCond %{REQUEST_FILENAME} !-f
7.  RewriteCond %{REQUEST_FILENAME} !-d
8.  RewriteRule . /index.php \[L\]

10.  # END WordPress

在上面添加如下内容:

1.  RewriteBase /wp-content/uploads/
2.  RewriteCond %{HTTP_REFERER} !^$ \[NC\]
3.  RewriteCond %{HTTP_REFERER} !ccc5.cc \[NC\]
4.  RewriteCond %{HTTP_REFERER} !photozero.net \[NC\]
5.  RewriteCond %{HTTP_REFERER} !zhuaxia.com \[NC\]
6.  RewriteCond %{HTTP_REFERER} !xianguo.com \[NC\]
7.  RewriteCond %{HTTP_REFERER} !google.com \[NC\]
8.  RewriteCond %{HTTP_REFERER} !feedburner.com \[NC\]
9.  RewriteCond %{HTTP_REFERER} !feedsky.com \[NC\]
10.  RewriteCond %{HTTP_REFERER} !baidu.com \[NC\]
11.  RewriteRule .*\\.(gif|jpg|png|rar)$ http://i.6.cn/cvbnm/00/70/5e/dfb8695041a1b3b0c50d8ab634fff20c.png \[L\]

添加后变为:

1.  # BEGIN WordPress

3.  RewriteEngine On
4.  RewriteBase /
5.  RewriteCond %{REQUEST_FILENAME} !-f
6.  RewriteCond %{REQUEST_FILENAME} !-d
7.  RewriteRule . /index.php \[L\]
8.  RewriteBase /wp-content/uploads/
9.  RewriteCond %{HTTP_REFERER} !^$ \[NC\]
10.  RewriteCond %{HTTP_REFERER} !ccc5.cc \[NC\]
11.  RewriteCond %{HTTP_REFERER} !photozero.net \[NC\]
12.  RewriteCond %{HTTP_REFERER} !zhuaxia.com \[NC\]
13.  RewriteCond %{HTTP_REFERER} !xianguo.com \[NC\]
14.  RewriteCond %{HTTP_REFERER} !google.com \[NC\]
15.  RewriteCond %{HTTP_REFERER} !feedburner.com \[NC\]
16.  RewriteCond %{HTTP_REFERER} !feedsky.com \[NC\]
17.  RewriteCond %{HTTP_REFERER} !baidu.com \[NC\]
18.  RewriteRule .*\\.(gif|jpg|png|rar)$ http://i.6.cn/cvbnm/00/70/5e/dfb8695041a1b3b0c50d8ab634fff20c.png  \[L\]

20.  # END WordPress

说明:

1.  RewriteCond %{HTTP_REFERER} !ccc5.cc \[NC\]
2.  RewriteCond %{HTTP_REFERER} !photozero.net \[NC\]
3.  RewriteCond %{HTTP_REFERER} !zhuaxia.com \[NC\]
4.  RewriteCond %{HTTP_REFERER} !xianguo.com \[NC\]
5.  RewriteCond %{HTTP_REFERER} !google.com \[NC\]
6.  RewriteCond %{HTTP_REFERER} !feedburner.com \[NC\]
7.  RewriteCond %{HTTP_REFERER} !feedsky.com \[NC\]
8.  RewriteCond %{HTTP_REFERER} !baidu.com \[NC\]
9.  RewriteRule .*\\.(gif|jpg|png|rar)$ http://i.6.cn/cvbnm/00/70/5e/dfb8695041a1b3b0c50d8ab634fff20c.png  \[L\]

如果访问图片的请求，来自上面的网站,则正常显示图片,否则直接转到一张指定的固定图片.

请将其中的ccc5.cc和http://i.6.cn/cvbnm/00/70/5e/dfb8695041a1b3b0c50d8ab634fff20c.png替换为自己域名和一张盗链提示图片

注:将修改后的.htaccess文件覆盖空间原文件,并将其权限设置为:444只读,既可以防止设置程序时被自动替换掉,还可以增加网站的安全性.

推荐图片外链网站（无水印 速度快） [http://tu.6.cn](http://tu.6.cn)

转载自: [WordPress防盗链方法 | 知更鸟](http://zmingcx.com/wordpress-the-most-convenient-method-of-image-hotlinking-prevention.html "本文固定链接 http://zmingcx.com/wordpress-the-most-convenient-method-of-image-hotlinking-prevention.html")