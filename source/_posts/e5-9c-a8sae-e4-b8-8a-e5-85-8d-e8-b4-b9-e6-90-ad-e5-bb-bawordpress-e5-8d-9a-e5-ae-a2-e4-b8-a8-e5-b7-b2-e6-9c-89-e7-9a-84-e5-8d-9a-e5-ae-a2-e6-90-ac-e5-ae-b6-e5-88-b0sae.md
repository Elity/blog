---
title: SAE上免费搭建Wordpress博客丨已有的博客搬家到SAE
tags:
  - SAE
  - Sina
  - wordpress
  - 搬家
  - 教程
url: 733.html
id: 733
categories:
  - 编程随想
date: 2011-10-01 20:45:46
---

 国庆没回去 ，又蛋疼了，又来折腾教程了

SAE是Sina App Engine 的简写，了解详情去参阅[百度百科](http://baike.baidu.com/view/2974259.htm)

我们首先去SAE首页注册一个帐号，当然如果你有新浪微博的话貌似可以直接登录

猛点下列地址去注册  注册过程就不写了

[http://sae.sina.com.cn/activity/invite/43918/weibo ](http://sae.sina.com.cn/activity/invite/43918/weibo)

填写完各项资料后，点击网站导航栏的应用商店

**（先说明，看不清的图片，点击图片会放大）**

[![](http://ccc5.cc/wp-content/uploads/2011/10/1-300x123.png "1")](http://ccc5.cc/wp-content/uploads/2011/10/1.png)

然后在下头选择安装应用，这里我们选择**Wordpress for sae** **，  **点击立即安装，然后如图选择

[![](http://ccc5.cc/wp-content/uploads/2011/10/2-300x68.png "2")](http://ccc5.cc/wp-content/uploads/2011/10/2.png)

二级域名那自己填，以后你的博客访问地址就是你所填的那样了，这里我填ccc5,既我的博客访问地址为ccc5.sinaapp.com

确认填好好点击安装到以上位置，然后等待安装结束，大概一分钟左右会安装完成

[![](http://ccc5.cc/wp-content/uploads/2011/10/3-300x115.png "3")](http://ccc5.cc/wp-content/uploads/2011/10/3.png)

进入初始化

[![](http://ccc5.cc/wp-content/uploads/2011/10/4-300x246.png "4")](http://ccc5.cc/wp-content/uploads/2011/10/4.png)

一切就绪后，点击安装就成了，假如您先前填的二级域名为xxx

此时，您可以通过 http://xxx.sinaapp.com访问您的博客了

当然，您也可以通过http://xxx.sinaapp.com/wp-admin/  访问您的博客后台了

至于后台的设置，主题及插件的安装就不在这篇文章的范畴了

**=============我是蛋疼分割线==============**

如果您本来就有一个WordPress博客，现在想搬家到SAE来，您可以按照以上步骤先安装好一个新的WordPress程序，

然后按如图操作

[![](http://ccc5.cc/wp-content/uploads/2011/10/5.png "5")](http://ccc5.cc/wp-content/uploads/2011/10/5.png)

然后来到数据库管理，找到我们刚刚安装生成的表，全部删除

[![](http://ccc5.cc/wp-content/uploads/2011/10/8-300x236.png "6")](http://ccc5.cc/wp-content/uploads/2011/10/8.png)

接下来导入你原来博客的数据库

[![](http://ccc5.cc/wp-content/uploads/2011/10/7-300x152.png "7")](http://ccc5.cc/wp-content/uploads/2011/10/7.png)

这里特别注意，数据库若导入成功，你后台的登录帐号密码全部变成和原来一样的了

（此时，数据库里面的链接都是原来域名的 此刻你需要到数据库管理软件执行SQL语句）

UPDATE '表名' SET '字段' = REPLACE('字段','待替换内容','替换值');

例如我原来的ccc5.cc/suifeng改为现在的ccc5.cc/suifeng

**UPDATE wp\_posts SET post\_content = replace(post_content, '[http://www.ccc5.cc/suifeng](http://www.ccc5.cc/suifeng)', '[http://www.ccc5.cc](http://www.ccc5.cc/)');**

**这样 你以后的图片才会正常显示**

这里可能时间有点长，等待完毕后，我们接下来剩下的就是搬原来博客的附件图片过来了

下载你原来博客目录下的wp-content文件夹，这个文件夹下有所有的用户数据

这里就需要参阅Sina提供的文档了，因为SAE提供的SVN仓库并不像一般虚拟主机的FTP服务器那样可以用FlashFXP之类的软件上传  ，必须使用SVN软件同步文件，先去下载  猛点此处去下载→ [TortoiseSVN客户端](http://sae.sina.com.cn/?m=devcenter&catId=33&content_id=129)

使用方法可以参阅Sina提供的教程，很详细了

[http://sae.sina.com.cn/?m=devcenter&catId=33&content_id=129](http://sae.sina.com.cn/?m=devcenter&catId=33&content_id=129)

这里记得别搞错了，你得仓库地址为  https://svn.sinaapp.com/xxx  (XXX为你的二级域名)

按照Sina的教程搞完，你的博客算是搬家完成了。。。

SO，我该去吃饭了，都九点了.......

**决定木有？     心动了就_[猛点我](http://sae.sina.com.cn/activity/invite/43918/weibo)_**