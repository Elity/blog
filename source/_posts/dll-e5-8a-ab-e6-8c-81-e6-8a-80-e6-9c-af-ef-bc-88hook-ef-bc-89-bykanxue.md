---
title: 'DLL劫持技术（Hook）    By:kanxue'
tags:
  - Hook，DLL劫持
  - 脱壳破解
url: 526.html
id: 526
categories:
  - 脱壳破解
date: 2011-09-01 14:22:33
---

说明

我是从CoDe_Inject帮助下才了解这个DLL劫持技术（或称HOOK），利用这个制作内存补丁非常的好用。Yonsm在几年前，写了个工具AheadLib，可以很方便地生成各类DLL头文件，见本帖2楼。**这种补丁方法，适合制作被ASProtect,Armadillo,Themida等各类强壳保护软件的补丁。**

  
  
当一个可执行文件运行时，Windows加载器将可执行模块映射到进程的地址空间中，加载器分析可执行模块的输入表，并设法找出任何需要的DLL，并将它们映射到进程的地址空间中。  
  
由于输入表中只包含DLL名而没有它的路径名，因此加载程序必须在磁盘上搜索DLL文件。首先会尝试从当前程序所在的目录加载DLL，如果没找到，则在Windows系统目录查找，最后是在环境变量中列出的各个目录下查找。利用这个特点，先伪造一个系统同名的DLL，提供同样的输出表，每个输出函数转向真正的系统DLL。程序调用系统DLL时会先调用当前目录下伪造的DLL，完成相关功能后，再跳到系统DLL同名函数里执行，如图18.4。这个过程用个形象的词来描述就是系统DLL被劫持（hijack）了。

[![](http://ccc5.cc/wp-content/uploads/2011/09/1-300x140.gif "1")](http://ccc5.cc/wp-content/uploads/2011/09/1.gif)

利用这种方法取得控制权后，可以对主程序进行补丁。此种方法只对除kernel32.dll, ntdll.dll等核心系统库以外的DLL有效，如网络应用程序的ws2_32.dll，游戏程序中的d3d8.dll，还有大部分应用程序都调用的lpk.dll，这些DLL都可被劫持。  
利用5.6.2章提供的CrackMeNet.exe来演示一下如何利用劫持技术制作补丁，目标文件用Themida v1.9.2.0加壳保护。  
  
**1．补丁地址**  
去除这个CrackMe网络验证方法参考第5章，将相关补丁代码存放到函数PatchProcess( )里。例如将401496h改成：

  

代码:

00401496 EB 29 jmp short 004014C1

补丁编程实现就是：  

代码:

 
unsigned char p401496\[2\] = {0xEB, 0x29};
WriteProcessMemory(hProcess,(LPVOID)0x401496, p401496, 2, NULL);

p401496这个数组的数据格式，可以用OllyDBG插件获得，或十六进制工具转换。例如Hex Workshop打开文件，执行菜单Edit/Copy As/Source即可得到相应的代码格式。  
  
**2．构建输出函数**  
查看实例CrackMeNet.exe输入表，会发现名称为ws2\_32.dll的DLL，因此构造一个同名的DLL来完成补丁任务。伪造的ws2\_32.dll有着真实ws2_32.dll一样的输出函数，完整源码见光盘。实现时，可以利用DLL模块中的函数转发器来实现这个目标，其会将对一个函数的调用转至另一个DLL中的另一个函数。可以这样使用一个pragma指令：  

代码:

#pragma comment(linker, "/EXPORT:SomeFunc=DllWork.someOtherFunc")

这个pragma告诉链接程序，被编译的DLL应该输出一个名叫SomeFunc的函数。但是SomeFunc函数的实现实际上位于另一个名叫SomeOtherFunc的函数中，该函数包含在称为DllWork. dll的模块中。  
如要达到劫持DLL的目的，生成的DLl输出函数必须与目标DLL输出函数名一样，本例可以这样构造pragma指令：  

代码:

#pragma comment(linker, "/EXPORT:WSAStartup=\_MemCode\_WSAStartup,@115")

编译后的DLL，会有与ws2\_32.dll同名的一个输出函数WSAStartup，实际操作时，必须为想要转发的每个函数创建一个单独的pragma代码行，读者可以写一个工具或用其他办法，将ws2\_32.dll输出函数转换成相应的pragma指令。  
  
当应用程序调用伪装ws2\_32.dll的输出函数时，必须将其转到系统ws2\_32.dl中去，这部分的代码自己实现。例如WSAStartup输出函数如下构造：  

代码:

 
ALCDECL MemCode_WSAStartup(void)
{
GetAddress("WSAStartup");
\_\_asm JMP EAX;//转到系统ws2\_32.dll的WSAStartup输出函数
}

其中GetAddress()函数的代码如下：  

代码:

 
// MemCode 命名空间
namespace MemCode
{
HMODULE m_hModule = NULL; //原始模块句柄
DWORD m_dwReturn\[500\] = {0}; //原始函数返回地址
// 加载原始模块
inline BOOL WINAPI Load()
{
TCHAR tzPath\[MAX_PATH\]={0};
TCHAR tzTemp\[MAX_PATH\]={0};
GetSystemDirectory(tzPath, sizeof(tzPath));
strcat(tzPath,"[\\\ws2\_32.dll");m\_hModule = LoadLibrary(tzPath);//加载系统系统目录下ws2\_32.dllif (m\_hModule == NULL){wsprintf(tzTemp, TEXT("无法加载 %s，程序无法正常运行。"), tzPath);MessageBox(NULL, tzTemp, TEXT("MemCode"), MB\_ICONSTOP);}return (m\_hModule != NULL); } // 释放原始模块inline VOID WINAPI Free(){if (m\_hModule)FreeLibrary(m\_hModule);}// 获取原始函数地址FARPROC WINAPI GetAddress(PCSTR pszProcName){FARPROC fpAddress;TCHAR szProcName\[16\]={0};TCHAR tzTemp\[MAX\_PATH\]={0}; if (m\_hModule == NULL){if (Load() == FALSE)ExitProcess(-1);} fpAddress = GetProcAddress(m\_hModule, pszProcName);if (fpAddress == NULL){if (HIWORD(pszProcName) == 0){wsprintf(szProcName, "%d", pszProcName);pszProcName = szProcName;}wsprintf(tzTemp, TEXT("无法找到函数 %hs，程序无法正常运行。"), pszProcName);MessageBox(NULL, tzTemp, TEXT("MemCode"), MB\_ICONSTOP);ExitProcess(-2);}return fpAddress;}}using namespace MemCode;]("file:)[]("file:)

编译后，用LordPE查看伪造的ws2\_32.dll输出函数，和真实ws2\_32.dll完全一样，如图18.5所示。[![](http://ccc5.cc/wp-content/uploads/2011/09/2-300x207.gif "2")](http://ccc5.cc/wp-content/uploads/2011/09/2.gif)

查看伪造的ws2_32.dll中任意一个输出函数，例如WSACleanup：

  

代码:

 
.text:10001CC0 ; int __stdcall WSACleanup()
.text:10001CC0 WSACleanup proc near
.text:10001CC0 push offset aWsacleanup ;"WSACleanup"
.text:10001CC5 call sub_10001000 ;GetAddress(WSACleanup)
.text:10001CCA jmp eax
.text:10001CCA WSACleanup endp

会发现输出函数WSACleanup()首先调用GetAddress(WSACleanup)，获得真实ws2\_32.dll中WSACleanup的地址，然后跳过去执行，也就是说ws2\_32.dll各输出函数被HOOK了。  
  
**3．劫持输出函数**  
ws2_32.dll有许多输出函数，经分析，程序发包或接包时，WSAStartup输出函数调用的比较早，因此在这个输出函数放上补丁的代码。代码如下：  

代码:

 
ALCDECL MemCode_WSAStartup(void)
{
hijack();
GetAddress("WSAStartup");
__asm JMP EAX; 
}
hijack()函数主要是判断是不是目标程序，如是就调用PatchProcess()进行补丁。
void hijack()
{
if (isTarget(GetCurrentProcess())) //判断主程序是不是目标程序，是则补丁之
{
PatchProcess(GetCurrentProcess());
}
}

伪造的ws2\_32.dll制作好后，放到程序当前目录下，这样当原程序调用WSASTartup函数时就调用了伪造的ws2\_32.dll的WSASTartup函数，此时hijack()函数负责核对目标程序校验，并将相关数据补丁好，处理完毕后，转到系统目录下的ws2_32.dll执行。  
这种补丁技术，对加壳保护的软件很有效，选择挂接的函数最好是在壳中没有被调用，当挂接函数被执行时，相关的代码己被解压，可以直接补丁了。有些情况下，必须用计数器统计挂接的函数的调用次数来接近OEP。此方法巧妙地绕过了壳的复杂检测，很适合加壳程序的补丁制作。  
一些木马或病毒也会利用DLL劫持技术搞破坏，因此当在应用程序目录下发现系统一些DLL文件存在时，应引起注意。  
  
Kanxue  
2008.3.8

向大牛致敬   沿着大牛们的足迹前进