---
title: Ubuntu 11.10之使用burg成功美化grub2
tags:
  - burg
  - Grub2
  - Ubuntu
url: 913.html
id: 913
categories:
  - 编程随想
date: 2011-10-15 06:31:21
---

_BURG_是一个基于GRUB2的启动管理器。详情请参考其在google的项目页面：[http://code.google.com/p/burg/](http://code.google.com/p/burg/)

第一步

添加源  执行

> sudo gedit /etc/apt/sources.list 

在其末尾添加

> deb [http://ppa.launchpad.net/bean123ch/burg/ubuntu](http://ppa.launchpad.net/bean123ch/burg/ubuntu) maverick main
> 
> deb-src [http://ppa.launchpad.net/bean123ch/burg/ubuntu](http://ppa.launchpad.net/bean123ch/burg/ubuntu) maverick main

第二步 当然是不能忘的

> sudo apt-get update

第三步update后会出现未导入公钥的错误，不要紧

> gpg --keyserver keyserver.ubuntu.com --recv 55708F1EE06803C5                  //下载密钥
> 
> gpg --export --armor 55708F1EE06803C5 | sudo apt-key add-                //导入密钥

第四步安装burg

> sudo apt-get install burg burg-themes burg-emu

上述命令安装了三个程序：burg主程序；burg-themes主题；burg-emu为burg模拟器

安装期间一路回车即可，有的需要输入y

第五步

> sudo burg-install "(hd0)"

这步是必须的，他会自动检测你所安装的系统生成burg.cfg文件

第六步

> update-burg

至此安装完成，在终端出入burg-emu就可预览到效果。

第七步

> gedit /boot/burg/burg.cfg 
> 
> F1帮助  F2切换主题  F3修改分辨率·····其余的继续F1··

修改启动系统的名字  自己看着改  个人觉得名字短点好看些

上些我的图  哈哈  弄了一宿  还是小有成就感的   

嘿嘿

[![](http://ccc5.cc/wp-content/uploads/2011/10/IMG_20111015_222636-300x225.jpg "IMG_20111015_222636")](http://ccc5.cc/wp-content/uploads/2011/10/IMG_20111015_222636.jpg)

[![](http://ccc5.cc/wp-content/uploads/2011/10/Screenshot-2011-10-15-063003-300x168.png "Screenshot-2011-10-15 06:30:03")](http://ccc5.cc/wp-content/uploads/2011/10/Screenshot-2011-10-15-063003.png)

[![](http://ccc5.cc/wp-content/uploads/2011/10/IMG_20111015_135157-300x225.jpg "IMG_20111015_135157")](http://ccc5.cc/wp-content/uploads/2011/10/IMG_20111015_135157.jpg)

当然 玩腻了可以卸载

> sudo apt-get remove burg burg-themes burg-emu