---
title: ubuntu中安装wine
tags:
  - Ubuntu
url: 471.html
id: 471
categories:
  - 编程随想
date: 2011-07-24 11:08:40
---

wine是一款用于虚拟使用windows的软件，你可以登陆官方网站，http://www.winehq.org/download 参看各种linux的安装方法，因为是国外的网站，都是英文，这里我只把在ubuntu中安装的方法翻译给大家，仅供参考，因为水平有限，如有错误请予指正，方便他人参考。  
在ubuntu中，是直接添加PPA源，然后再新立得软件管理器中安装，首先添加PPA源，依次打开：系统->系统管理->软件源，输入系统密码，如图所示窗口：(注如果你的ubuntu10.10，可能会找不到“软件源”，需要设置菜单，方法是把鼠标停留在“应用程序”上面，鼠标右键弹出菜单选择“编辑菜单”，然后再窗口左边选择“系统管理”，勾选“软件源”就可以了)

[![](http://ccc5.cc/wp-content/uploads/2011/07/1.png "1")](http://ccc5.cc/wp-content/uploads/2011/07/1.png)

如图位置选择，如果是中文版，则选择“其他软件”，点击“添加”：  
`ppa:ubuntu-wine/ppa`  
然后你就可以在新立得里面安装了。

你也可以使用如下命令在终端安装，更为方便：  
添加[PPA](http://imcn.me/html/y2011/3695.html "PPA"):  
`sudo add-apt-repository ppa:ubuntu-wine/ppa`

更新源：  
`sudo apt-get update`

安装：  
`sudo apt-get install wine1.3`