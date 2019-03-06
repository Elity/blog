---
title: PEDIY之----给记事本添加额外功能
tags:
  - OD
  - PE
  - 逆向
url: 1014.html
id: 1014
categories:
  - 脱壳破解
date: 2011-11-05 23:51:21
---

作 者:newjueqi

链 接: http://bbs.pediy.com/showthread.php?t=81208

=========原创信息还是必须保留滴  敬重技术达人=============

 具体的思路：其实自动保存功能就是程序隔一段时间保存一次，而记事本程序是自带了保存功能的，所以只要编程安装一个记时器，每隔一段时间模拟点击“保存菜单”，就能实现自动保存，最后退出时把记时器撤销。

首先，用eXeScope给记事本增加两个菜单项“自动保存”“关闭自动保存”，如图1所示：

[![](http://ccc5.cc/wp-content/uploads/2011/11/1-300x182.jpg "1")](http://ccc5.cc/wp-content/uploads/2011/11/1.jpg)

图1

其中，“自动保存”的菜单ID是8 ,“关闭自动保存” 的菜单ID是13。

完成后的菜单选项如图2所示：

[![](http://ccc5.cc/wp-content/uploads/2011/11/2.jpg "2")](http://ccc5.cc/wp-content/uploads/2011/11/2.jpg)

 图2

本次修改要用到3个window API函数: SetTimer, KillTimer , SendMessageA, 这3个API没包括在记事本的输入表里，所以要用LordPE 在输入表里增加这3个API。

用LordPE打开记事本程序，依次选择Direction->输入表的“…”按钮¬->点击右键->add import，出现如图3的界面，就可以在输入表里增加新的API

[![](http://ccc5.cc/wp-content/uploads/2011/11/3-300x164.jpg "3")](http://ccc5.cc/wp-content/uploads/2011/11/3.jpg)

图三

完成后就新增了一个区段Slivana如图4，放的就是新的IID数据，如图4所示

[![](http://ccc5.cc/wp-content/uploads/2011/11/4-300x102.jpg "4")](http://ccc5.cc/wp-content/uploads/2011/11/4.jpg)

图4

添加完新的API后又有一个新的问题了，那就是要确定新的API地址，这个在PEDIY的过程中是要自己解决的，而平时是编译器帮忙的。获得API地址有两个方法：

（1）直接查看新增的IID数据，载入程序后在命令行打入d 1013000，如图5所示：

[![](http://ccc5.cc/wp-content/uploads/2011/11/5-300x73.jpg "5")](http://ccc5.cc/wp-content/uploads/2011/11/5.jpg)

图5

从IAT与INT的对应关系可得

SetTimer的地址=\[0101302C \]

MessageBoxA的地址=\[ 01013030\]

KillTimer的地址=\[01013034 \]

(2) 在ＯＤ中，按Ctrl+N组合键，就能看到API地址的位置，如图6的红框部分所示

[![](http://ccc5.cc/wp-content/uploads/2011/11/6-300x45.jpg "6")](http://ccc5.cc/wp-content/uploads/2011/11/6.jpg)

图6

 由于PEDIY一般都是在默认的窗口过程前先处理我们定义的消息，所以接下来分析一下记事本程序的流程，看看记事本的窗口过程地址。众所周知，在一个标准的窗口程序中，必须要注册窗口类，函数原型如下：

ATON RegisterClassEx（CONST WNDCLASSEX *Ipwcx）；

其中WNDCLASSEX *Ipwcx是个结构体指针，定义如下：

WNDCLASSEX STRUCT DWORD 

       cbSize DWORD ? 

       style DWORD ? 

       lpfnWndProc DWORD ? 

       cbClsExtra DWORD ? 

       cbWndExtra DWORD ? 

       hInstance DWORD ? 

       hIcon DWORD ? 

       hCursor DWORD ? 

       hbrBackground DWORD ? 

       lpszMenuName DWORD ? 

       lpszClassName DWORD ? 

       hIconSm DWORD ? 

WNDCLASSEX ENDS 

lpfnWndProc就是窗口过程的地址，所以我们只需要在RegisterClassEx下中断，就能知道窗口过程的地址，在ＯＤ中如图７所示：

[![](http://ccc5.cc/wp-content/uploads/2011/11/7-300x109.jpg "7")](http://ccc5.cc/wp-content/uploads/2011/11/7.jpg)

图7

从图7可看到1003429就是系统默认的窗口过程的地址。

 在开始写代码前我们先简单回顾一下的windows的窗口过程函数和一般函数调用的过程，因为我们在后面的实际编码过程中必须要对这两个方面的知识有一定的了解。

窗口过程的原型为

wondowPrco proc hwnd,uMsg, wParam, lParam

hwnd: 窗口的句柄，可通告CreateWindowEx函数的返回值获取

uMsg: 消息的标识，一般以WM开头

wParam, lParam: 消息的两个参数

 当我们点击某个菜单后就产生了WM\_COMMAND消息，对于菜单引发的的WM\_COMMAND消息, lParam 的值为0，wParam的低16位是命令ID，高16位是通知码，菜单消息的通知码是0。

当调用一个_stdcall函数的时候，首先是函数的参数按从右到左的顺序入栈，然后是函数的返回地址入栈，一般函数调用的开始有“push ebp  mov ebp,esp”这句，所以我们进入窗口过程后就能得到类似于图8的堆栈结构

[![](http://ccc5.cc/wp-content/uploads/2011/11/8-300x108.jpg "8")](http://ccc5.cc/wp-content/uploads/2011/11/8.jpg)

图8

> 0006FDE0  /0006FE0C    ; ebp的值
> 
> 0006FDE4  |77D18734   ； 返回到 user32.77D18734，返回地址值
> 
> 0006FDE8  |00100618    ；窗口的句柄
> 
> 0006FDEC  |00000111    ；WM_COMMAND的16进制值，可查阅WinUser.h文件获得
> 
> 0006FDF0  |00000008    ；wParam，“自动保存”的菜单ID是8
> 
> 0006FDF4  |00000000    ；lParam，

由此可知，\[ebp+c\]是消息的类型，\[ebp+10\]是wParam参数，获取菜单的ID就需要知道wParam参数。

  另外还有一个问题要解决，SetTimer和KillTimer这两个API函数的参数中必须要传入窗口句柄作为参数，但窗口句柄怎么找呢？我们知道，在用CreateWindowExW函数创建窗口后会返回一个窗口句柄，在OD里对CreateWindowExW下断点，中断后代码如图9：

[![](http://ccc5.cc/wp-content/uploads/2011/11/9-300x123.jpg "9")](http://ccc5.cc/wp-content/uploads/2011/11/9.jpg)

图9

从红框部分可知，窗口句柄保存在地址\[1009830\]。

我们要模拟点击“保存”菜单，可以通过SendMessageW函数向窗口过程发送菜单消息，SendMessageW的原型如下：

LRESULT SendMessage（HWND hWnd，UINT Msg，WPARAM wParam，LPARAM IParam）；

参数：

hWnd就是窗口句柄，从上面分析可知，保存在\[1009830\]地址上。

Msg是要发送的消息类型，查阅WinUser.h文件可知，WM_COMMAND为0x111

wParam的低16位是命令ID，高16位是通知码，菜单消息的通知码是0，通过eXeScope可查知“保存”的菜单ID 为3，所以wParam参数为3。

lParam，对于菜单消息来说，为0。

  接下来就是实际的代码，本人选择在0100874F处添加代码，所以要把图7红框部分的1003429改为0100874F。

下面是写入的代码：

> 0100874F   .  55            push    ebp
> 
> 01008750   .  8BEC          mov     ebp, esp
> 
> 01008752   .  60            pushad  
> 
> 01008753   .  817D 0C 11010>cmp     dword ptr \[ebp+C\], 111  ；先比较消息的类型，看是否WM\_COMMAND, 如果是WM\_COMMAND的话再检查wParam参数进一步确定是哪个菜单的消息
> 
> 0100875A   .  75 0C         jnz     short 01008768    ；如果不是WM\_COMMAND消息的话就检查是否WM\_TIMER或WM_CLOSE消息
> 
> 0100875C   .  837D 10 08    cmp     dword ptr \[ebp+10\], 8  ；比较菜单的ID号，看是否“自动保存”的菜单ＩＤ
> 
> 01008760   .  74 1C         je      short 0100877E    ；不是的话就跳去检查其它的消息类型
> 
> 01008762   .  837D 10 0D    cmp     dword ptr \[ebp+10\], 0D    ；比较菜单的ID号，看是否“关闭自动保存”的菜单ＩＤ
> 
> 01008766   .  74 48         je      short 010087B0    ；不是的话就跳去检查其它的消息类型
> 
> 01008768   >  817D 0C 13010>cmp     dword ptr \[ebp+C\], 113  ；比较消息的类型是否是WM_TIMER消息，响应定时器消息
> 
> 0100876F   .  74 5F         je      short 010087D0    ；不是的话就跳去函数的结束清场工作
> 
> 01008771   .  837D 0C 10    cmp     dword ptr \[ebp+C\], 10　　；比较消息的类型是否是WM_CLOSE消息，闭关时要先撤销定时器
> 
> 01008775   .  74 39         je      short 010087B0    ；不是的话就跳去函数的结束清场工作
> 
> 01008777   >  61            popad
> 
> 01008778   .  5D            pop     ebp
> 
> 01008779   .^ E9 ABACFFFF   jmp     01003429  ；跳回原来记事本的消息循环
> 
> 0100877E   >  C605 20890001>mov     byte ptr \[1008920\], 1  ；标记已使用自动保存功能
> 
> 01008785   .  6A 00         push    0          ; /Timerproc = NULL
> 
> 01008787   .  68 60EA0000   push    0EA60      ; |Timeout = 60000. ms
> 
> 0100878C   .  6A 01         push    1           ; |TimerID = 1
> 
> 0100878E   .  FF35 30980001 push    dword ptr \[1009830\]     ; |hWnd =  \[1009830\]，在CreateWindowExW后保存窗口句柄的地址
> 
> 01008794   .  FF15 2C300101 call    dword ptr \[<&user32.SetTimer>\]   ; \\SetTimer
> 
> 0100879A   .  6A 00         push    0                 ; /Style = MB\_OK|MB\_APPLMODAL
> 
> 0100879C   .  68 00DB0001   push    0100DB00         ; |Title = "Note"
> 
> 010087A1   .  68 10DB0001   push    0100DB10          ; |Text = "AutoSave Start!"
> 
> 010087A6   .  6A 00         push    0               ; |hOwner = NULL
> 
> 010087A8   .  FF15 30300101 call    dword ptr \[<&user32.MessageBoxA>>; \\MessageBoxA  ；提示已使用自动保存功能
> 
> 010087AE   .^ EB C7         jmp     short 01008777  ；跳去函数结束清场
> 
> 010087B0   >  803D 20890001>cmp     byte ptr \[1008920\], 0  ；先比较是否已启动定时器
> 
> 010087B7   .^ 74 BE         je      short 01008777    ；如果没启动定时器就跳去函数结束清场
> 
> 010087B9   .  C605 20890001>mov     byte ptr \[1008920\], 0  ；把启动定时器的标记恢复
> 
> 010087C0   .  6A 01         push    1                 ; /TimerID = 1
> 
> 010087C2   .  FF35 30980001 push    dword ptr \[1009830\]    ; |hWnd，在CreateWindowExW后保存窗口句柄的地址
> 
> 010087C8   .  FF15 34300101 call    dword ptr \[<&user32.KillTimer>\]  ; \\KillTimer，撤销定时器
> 
> 010087CE   .^ EB A7         jmp     short 01008777  ；跳去函数结束清场
> 
> 010087D0   >  6A 00         push    0                  ; /lParam = 0
> 
> 010087D2   .  6A 03         push    3                ; |wParam = 3 ，“保存”菜单的ＩＤ号
> 
> 010087D4   .  68 11010000   push    111              ; |Message = WM_COMMAND
> 
> 010087D9   .  FF35 30980001 push    dword ptr \[1009830\]      ; |hWnd ，在CreateWindowExW后保存窗口句柄的地址
> 
> 010087DF   .  FF15 40120001 call    dword ptr \[<&USER32.SendMessageW>; \\SendMessageW  ；发送点击菜单“保存”的信号给记事本窗口，实现保存功能。
> 
> 010087E5   .^ EB 90         jmp     short 01008777    ；跳去函数结束清场