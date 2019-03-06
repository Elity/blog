---
title: 继续折腾burg的修复问题
tags:
  - burg
  - Linux
  - Ubuntu
  - 修复
url: 1786.html
id: 1786
categories:
  - 编程随想
date: 2013-05-26 16:37:10
---

一直用burg引导ubuntu和windows,前几天开机发现莫名其妙丢失了引导，机子自检完毕理应出现：

burg is loading....

但卡在那个命令出现之前的命令行，然后几秒钟后又自动重启了

那天被毕业设计烦得不行，就没去修复了，拿个写了带windows引导的u盘直接进入了windows

今天周末，闲着蛋疼，然后觉着每天用u盘启动系统也不是个长久之法，决定修复burg

以前自己修复过,但一直觉得挺麻烦..开个[传送门](http://www.ccc5.cc/266.html)

今天无非是整合以前的方法，思路：

直接下个grub2引导的镜像，进入你的linux系统，然后重新安装下burg

1.grub2引导镜像下载地址：[http://pan.baidu.com/share/link?shareid=487510&uk=369340502](http://pan.baidu.com/share/link?shareid=487510&amp;uk=369340502)

2.用U盘量产工具写入u盘

3.重启选择U盘引导

4.来到grub2界面  按C进入命令行模式

逐行输入如下命令即可进入系统上的linux，其中(hdx,y)  sdzn 中的 x、y、z、n根据自己磁盘的实际情况修改

set root=(hd0,8)
linux /boot/vmlinuz(按Tab键补全) root=/dev/sdb8
initrd /boot/initrd(按Tab键补全)
boot

如果一切顺利的话,应该是可以顺利进入linux了，如果(hdx,y)输入有误，下面的命令是无法按Tab键补全的

其它还有问题的话就是 /dev/sdzn 的问题了

5.来到linux,打开终端，输入  

sudo burg-install "(hd0)"

6.重启....又见到熟悉的burg界面啦