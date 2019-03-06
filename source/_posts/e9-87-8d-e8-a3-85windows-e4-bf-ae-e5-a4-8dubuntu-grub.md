---
title: 重装windows修复Ubuntu Grub
tags:
  - Grub2
  - Linux
url: 447.html
id: 447
categories:
  - 编程随想
date: 2011-06-30 04:06:10
---

当我们的脆弱的Windows定期瘫痪，无法修复只能重新安装的尴尬时刻，我们发现“霸道”的Windows 在安装时丝毫没有顾及在硬盘的MBR上还存在操作系统的引导程序（grub），直接删除，只写入Windows的引导信息。

不管微软这样是不是有意而为，这都看出Windows的陋习和微软的强盗行径。我们怎么办，放弃Windows？但是我们还要我们心爱的游戏，反正Windows已经是我硬盘上的一个因为游戏还存在的附属系统，我们忍了。

发扬Linux人的DIY精神，我们自己动手找回丢失的Linux引导程序。

1，使用Ubuntu live关盘引导进入 Ubuntu live 模式

2，在Ubuntu中运行终端，执行下列命令：

$sudo grub

其实，grub不仅只在我们引导时候出现在我们眼前，我们平时也可以直接运行grub命令进入模拟grub引导程序。

3，之后，我们在grub 命令行模式下接着执行

root (hdx,y)

**注意：**其中x为硬盘号，数字 “ 0 ”指我们机器上的第一块硬盘，grub的排列顺序是从 “ 0 ”开始的。同理，如果你的系统在第二个硬盘上则是1，依次类推。另外，其中的“ y ”表示前面所指硬盘的 /boot 所在分区号，如果实在不知道，后面信息怎么填，你可以在输入逗号后按Tab键，这样可列出硬盘的分区信息。

4，安装 grub ，执行命令：

setup (hdx)

setup (hdx)就是把GRUB写到"x"硬盘的MBR上。如果成功会有一个successful...... 

5，安成安装后，退出 grub 。

quit

6，重启机器，重新引导Linux。

$sudo reboot

**备注：**其实，当我们安装完毕grub之后，还需要检查 grub 的配置文件信息是否正确，方法是在 Ubuntu live 模式下利用 mount 命令加载原来ubuntu 的分区，在原 /boot 目录下的 grub/menu1.st 文件中的信息。