---
title: 为某些软件加皮肤
tags:
  - diy
  - 皮肤
  - 软件
url: 1257.html
id: 1257
categories:
  - 脱壳破解
date: 2012-03-08 14:51:26
---

有些软件的默认界面实在太搓了   但苦于软件作者的审美观与软件制造水平实在不成正比  各位童鞋只能默默的忍

今天在一蓑烟雨论坛闲逛偶然发现了这个帖子  故转来

需准备工具 （猛戳下面去下载）

#### [LordPE Deluxe](http://www.onlinedown.net/soft/44301.htm)

然后需要的俩dll和she皮肤我会打包     更多she皮肤请自行百度

开工吧

把俩DLL程序和那个skinh.she皮肤放到需要修改皮肤的EXE文件目录下

先运行EXE  开始是没效果（有点废话）

![](http://pic.yupoo.com/a408115319/BNDa7tv1/VtI66.png "1")

然后运行上面告诉你下载的工具

按下图箭头所示步骤操作

![](http://pic.yupoo.com/a408115319/BNDeawPM/MNePP.png "2")

然后按照如图填写相应的导入信息

![](http://pic.yupoo.com/a408115319/BNDealsK/QyQbM.jpg "3")

点完加号后确认  然后关掉导入表  点保存确认  再来个保存确认就OK了

再运行EXE看看

![](http://pic.yupoo.com/a408115319/BNDeaAuH/xLObQ.png "4")

好了  具体就是这样了

如果想要更换别的皮肤  只要去百度搜SHE皮肤下载  重命名为 skinh.she 覆盖现在这个就可以了  

下面附上我收藏的一些she皮肤以及两个调用皮肤的DLL

[猛戳我](http://115.com/file/e7lm7jpg)

如果您想自己更换别的DLL接口函数名并且有点C++基础的话  这里附上skin.dll的C++源码

感谢一蓑烟雨 vienna_ vienna _[http://www.unpack.cn/thread-47495-1-4.html](http://www.unpack.cn/thread-47495-1-4.html) 的源码

`#include #include "SkinH.h" #pragma comment (lib,"SkinH.lib") extern "C" _declspec(dllexport) void Hello(); BOOL APIENTRY DllMain( HANDLE hModule, DWORD ul_reason_for_call, LPVOID lpReserved ) { switch(ul_reason_for_call) { case DLL_PROCESS_ATTACH: Hello(); break; case DLL_THREAD_ATTACH: case DLL_THREAD_DETACH: case DLL_PROCESS_DETACH: SkinH_DetachEx(NULL); break; } return TRUE; } void Hello() { #include #include "SkinH.h" #pragma comment (lib,"SkinH.lib") extern "C" _declspec(dllexport) void Hello(); BOOL APIENTRY DllMain( HANDLE hModule, DWORD ul_reason_for_call, LPVOID lpReserved ) { switch(ul_reason_for_call) { case DLL_PROCESS_ATTACH: Hello(); break; case DLL_THREAD_ATTACH: case DLL_THREAD_DETACH: case DLL_PROCESS_DETACH: SkinH_DetachEx(NULL); break; } return TRUE; } void Hello() { //皮肤效果都往这里加~~ SkinH_Attach(); }`