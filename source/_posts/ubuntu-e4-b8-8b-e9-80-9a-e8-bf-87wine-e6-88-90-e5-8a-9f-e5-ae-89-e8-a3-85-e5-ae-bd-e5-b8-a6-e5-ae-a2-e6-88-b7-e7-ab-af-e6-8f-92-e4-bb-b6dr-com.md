---
title: ubuntu下通过wine成功安装宽带客户端插件Dr.com
tags:
  - Ubuntu
  - wine
  - 宽带插件
url: 918.html
id: 918
categories:
  - 编程随想
date: 2011-10-15 07:40:19
---

首先当然是安装wine1.3   可以参考这  [http://ccc5.cc/471.html](http://ccc5.cc/471.html)

如果此时就安装插件的话80%以上还是不会成功

那么此时您应该下载改插件必须的动态链接库放入Wine模拟的C:\\windows\\system32下面

我这打包上传了 [http://dl.dbank.com/c0i58x53qh](http://dl.dbank.com/c0i58x53qh)

里头有8个dll文件  解压后全部丢进去  然后安装插件   每次开机手动运行ishare_user.exe  然后放心拨号吧