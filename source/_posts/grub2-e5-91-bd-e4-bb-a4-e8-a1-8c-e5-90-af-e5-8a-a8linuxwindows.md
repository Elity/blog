---
title: grub2命令行启动Linux/Windows
tags:
  - gurb2
  - Linux
  - Ubuntu
  - windows
url: 1643.html
id: 1643
categories:
  - 编程随想
date: 2012-12-23 18:14:50
---

新硬盘只装了个win8,今天本想装ubuntu12.04,一切顺利，安装完成后重启.....问题来了

他喵的直接进了win8...这不科学啊  

进入ubuntu的LiveCD，按照此法[http://www.ccc5.cc/266.html](http://www.ccc5.cc/266.html) ，试图重新修复下grub2.....

命令执行到最后居然出现警告了

杯了个具，重启果然只剩Grub:加个光标在跳了...

幸好上次出现问题时记录了几个关键命令，顺利用命令进入了win8(ubuntu依然无法进入，等下重装试试)

现在记录下这几个命令，或许以后还用得到(毕竟网上到处都是grub的，而grub2的不是很多)

启动Linux

set root=(hd0,8)
//设置根分区,当然这是/分区和/boot在挂在同一分区的情况 (hdx,y)根据自己情况设置
//sdrs 也根据自己情况设置
 
linux /boot/vmlinuz....(此处按Tab键补全) root=/dev/sdb8
  
initrd /boot/initrd(继续Tab键补全)
  
boot
//启动

启动Windows

set root=(hd0,0)
//设置windows系统所在分区
chainloader +1
boot