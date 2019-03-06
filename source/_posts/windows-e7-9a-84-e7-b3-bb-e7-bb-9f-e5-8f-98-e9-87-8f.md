---
title: windows的系统变量
tags:
  - windows
  - 系统变量
url: 1884.html
id: 1884
categories:
  - 编程随想
date: 2013-10-18 15:35:41
---

   一个月没更新博客了。。今天来写写

   在使用win7(8)时，我经常需要进入C:\\Users\\【用户名】\\AppData\\Roaming下，但AppData文件是属于隐藏文件，我们通过资源管理器访问的话，得修改系统的文件隐藏选项才能进入该文件，但更多的情况下我习惯在按下Win+R打开“运行”后输入“%AppData%”回车。

   “%AppDate%”就是Windows系统变量，经常用到的还有%temp%、%windir%……

    无聊之余搜索了一下资料，发现只需在命令行下输入“set”即可例出这些系统变量，结果如下（系统为win8企业版）：

ALLUSERSPROFILE=C:\\ProgramData
APPDATA=C:\\Users\\【用户名】\\AppData\\Roaming
CommonProgramFiles=C:\\Program Files\\Common Files
CommonProgramFiles(x86)=C:\\Program Files (x86)\\Common Files
CommonProgramW6432=C:\\Program Files\\Common Files
ComSpec=C:\\Windows\\system32\\cmd.exe
HOMEDRIVE=C:
HOMEPATH=\\Users\\【用户名】
LOCALAPPDATA=C:\\Users\\【用户名】\\AppData\\Local
ProgramData=C:\\ProgramData
ProgramFiles=C:\\Program Files
ProgramFiles(x86)=C:\\Program Files (x86)
ProgramW6432=C:\\Program Files
PSModulePath=C:\\Windows\\system32\\WindowsPowerShell\\v1.0\\Modules\
PUBLIC=C:\\Users\\Public
SystemDrive=C:
SystemRoot=C:\\Windows
TEMP=C:\\Users\\【用户名】\\AppData\\Local\\Temp
TMP=C:\\Users\\【用户名】\\AppData\\Local\\Temp
USERPROFILE=C:\\Users\\【用户名】
windir=C:\\Windows

当然,您如果想自定义变量可以应用“setx”命令实现，格式：

setx 【变量名】 【路径】

或者在系统属性---高级----环境变量中添加