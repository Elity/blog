---
title: nw.js调用dll
tags:
  - dll
  - node.js
  - nw.js
url: 2133.html
id: 2133
categories:
  - 编程随想
date: 2017-04-01 12:38:36
---

忙乎了一天半，踩了N多坑，总算把https://github.com/essa/nw\_native\_dll\_sample这个nw.js调用dll的示例跑起来了。 跑起来后想想，踩坑的很大原因是自己看文档不仔细和之前安装的各种新版本(有些可能还是预览版本)的依赖比如node.js v7、python3.6。 一一例举下这些坑，及我的解决办法： 1.自己最开始没有装c的编译环境，便把示例里的.c和.h文件发给c++开发的同事编译，发给我后，测试代码始终出错，后发现同事编译的是x64版本，而我这边node是32位的。 = =！ 后台干脆自己装MinGw编译了.nodejs调用DLL的测试总算跑通了。 2.然后在示例里面跑nw.js去调用dll始终没反应，然后发现无论是F12还是win.showDevTools()都无法打开控制台。。。再次跑去nw.js官网查文档，发现原来nw.js的build方式分为normal和SDK。只有SDK版本才带控制台。npm install nw --nwjs\_build\_type=sdk 和cnpm install nw --nwjs\_build\_type=sdk都巨慢，挂着下载一晚上第二天来还是个报错。。只得自己去github下载压缩包 然后新建一个目录www，在www目录下新建一个以下载的安装包版本号命名的文件夹，比如我的0.21.4,然后把压缩包原封不动（不要改名）放到文件夹内。 然后在www内 用http-server -p 9999 开启个本地服务，在项目根目录新建文件 .npmrc，内容为： nwjs\_build\_type=sdk NWJS\_URLBASE=http://localhost:9999/ 然后在根目录npm install nw --save; 3.项目目录nw app 运行程序，控制台终于出来了，发现错误来源于require('ffi')与require('ref'); 照着错误一番搜索，发现直接npm install 这样安装的方式并不适应于nw.js调用(node.js直接调用是没得问题的)。需要用nw-gyp 在对应的模块文件文件夹内rebuild，见官方[doc](http://docs.nwjs.io/en/latest/For%20Users/Advanced/Use%20Native%20Node%20Modules/)。于是乎，来到ffi模块的目录内，执行命令： nw-gyp rebuild --target=0.21.4 --arch=ia32 十分钟，二十分钟，半小时。。都没得反应，一小时后终于有反应了，说服务器无响应了。。哔了狗了 照着错误提示的地址，用迅雷把压缩包拉下来：http://node-webkit.s3.amazonaws.com/v0.21.4/nw-headers-v0.21.4.tar.gz 然后在原来的www目录新建目录v0.21.4,把下载的压缩包放到该文件下，然后nw-gyp rebuild --target=0.21.4 --arch=ia32 --dist-url=http://localhost:9999/ (target是nw.js版本 arch是cpu位，ia32或x64)。 然后报错找不到nw.lib、node.lib、x64/nw.lib、x64/node.lib。同样去下载下载，然后再次执行nw-gyp rebuild --target=0.21.4 --arch=ia32 。successful！！然后去ref目录同样操作。 4.再次回到项目目录 nw app 总算跑起来了。