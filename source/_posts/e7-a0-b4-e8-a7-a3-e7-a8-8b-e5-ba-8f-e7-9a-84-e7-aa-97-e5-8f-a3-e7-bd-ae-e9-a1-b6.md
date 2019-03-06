---
title: 破解程序的窗口置顶
tags:
  - OD
  - 窗口置顶
url: 2055.html
id: 2055
categories:
  - 编程随想
date: 2015-12-01 00:52:49
---

今天拿到一个程序，运行即全屏+置顶，很是烦人，不方便用第三方软件查看其网络访问情况，用各类资源工具改了半天没得效果。

后来用OD调试了一番，发现其置顶效果并非通过CreateWindowEx(WS\_EX\_TOPMOST, ......)实现，那么可能是通过SetWindowPos(hwnd,HWND\_TOPMOST,0,0,0,0,SWP\_NOMOVE|SWP_NOSIZE)来实现的。第二个参数HWND_TOPMOST的值为0x-1,在OD下为FFFFFFFF.

我们将其修改为其它非该值的任意值即可。

0045C5E0  |. /74 03         je XXX程序.0045C5E5                        ;  这里跳过即刻去掉窗口置顶
0045C5E2  |. |8B40 1C       mov eax,dword ptr ds:\[eax+0x1C\]          ;  这里会给eax赋值-1  即topmost
0045C5E5  |> \\FF75 1C       push \[arg.6\]                             ; /Flags
0045C5E8  |.  FF75 18       push \[arg.5\]                             ; |Height
0045C5EB  |.  FF75 14       push \[arg.4\]                             ; |Width
0045C5EE  |.  FF75 10       push \[arg.3\]                             ; |Y
0045C5F1  |.  FF75 0C       push \[arg.2\]                             ; |X
0045C5F4  |.  50            push eax                                 ; |设置SetWindowPos的第二个参数为eax的值
0045C5F5  |.  FF71 1C       push dword ptr ds:\[ecx+0x1C\]             ; |hWnd
0045C5F8  |.  FF15 60D44600 call dword ptr ds:\[<&USER32.SetWindowPos>; \\SetWindowPos