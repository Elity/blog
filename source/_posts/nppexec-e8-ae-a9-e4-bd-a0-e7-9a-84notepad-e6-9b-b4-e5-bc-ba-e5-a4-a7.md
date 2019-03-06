---
title: NppExec让你的Notepad++更强大
tags:
  - Notepad++
url: 2075.html
id: 2075
categories:
  - 编程随想
date: 2016-05-31 11:27:51
---

NppExec支持一些命令，可以让我们实现许多自定义需求，具体的命令可以参考其帮助文件。

今天参考了帮助文件，写了这个脚本，让其根据文件后缀，自动选择以什么方式执行该文件。

python，php，nodejs等脚本语言，甚至于需要编译的java，c等语言文件都可以实现一键运行。

以下是我写的：

NPP_SAVE
cd $(CURRENT_DIRECTORY)
if $(EXT_PART) == .js GOTO JS
if $(EXT_PART) == .py GOTO PYTHON
if $(EXT_PART) == .php GOTO PHP
GOTO EXIT
:JS
node $(FULL\_CURRENT\_PATH)
GOTO EXIT
 
:PYTHON
python $(FULL\_CURRENT\_PATH)
GOTO EXIT
 
:PHP
php $(FULL\_CURRENT\_PATH)
GOTO EXIT
 
:EXIT

命令很简单，但着实方便了我们即时运行代码