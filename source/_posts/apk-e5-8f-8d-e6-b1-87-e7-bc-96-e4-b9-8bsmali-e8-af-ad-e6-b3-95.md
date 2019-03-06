---
title: apk反汇编之smali语法
tags:
  - amail语法
  - apk
  - 安卓
url: 1894.html
id: 1894
categories:
  - 脱壳破解
date: 2014-01-09 22:56:10
---

### ![](http://ww4.sinaimg.cn/large/6d1c4ea2gw1ecdoiyxvkcj20cg0b4wel.jpg)

### 类型

Dalvik的字节码中拥有两个主要的类型:基类和引用类型。引用类型

引用类型是对象和数组，其他的一切都是基类

基类被一个简单的字符描述。我没有提出这些缩写词———他们实际以字符串的形式存储于dex文件中

他们被定义与dex格式网页文档中（在AOSP库中的路径是dalvik/docs/dex-format.html）

V  空类型---仅仅可以用来作为返回类型

Z  Boolean 布尔型

B  Byte字节型

S  Short短整型（16位）

C  Char字符型

I  Int 整形

J  long (64 bits)长整型（64位）

F  Float浮点型

D  double (64 bits)双精度型（64位）

对象采用这样的形式Lpackage/name/ObjectName---开始的L表明这是一个对象类型，package/name/就是该对象，对象名是是对象的名称，并且分号表明对象名的结束。这个等同于java语言中的package.name.ObjectName结构，举个更具体的例子，“ Ljava/lang/String;”就等同于java语言的“java.lang.String”。数组采用\[I的形式—这代表一个一维整形数组就像java语言中的int\[\]。而多维数组，你简单的增加字符‘\[’就行了，例如\[\[I=int\[\]\[\](最大的维度是255).你也可以使用数组对象，例如\[Ljava/lang/String就是一个字符串数组。

### 方法

方法总是被定义为一个非常复杂的包括方法，方法名，参数类型和返回值的形式。

所有的这些信息被要求对于虚拟机而言可以找到正确的方法并且能够表现字节码上的静态分析（为了确认挥着选择最优化）。他们采用如下形式

Lpackage/name/ObjectName;->MethodName(III)Z

在这个例子中，你应该识别出“ Lpackage/name/ObjectName; 是一个类， MethodName明显是一个方法名，(III)Z是方法的签名，‘III’在这个例子中是三个整形参数，Z是表示返回一个布尔类型的返回值。

They take the form

Lpackage/name/ObjectName;->MethodName(III)Z

In this example, you should recognize Lpackage/name/ObjectName; as a type. MethodName is obviously the name of the method. (III)Z is the method's signature. III are the parameters (in this case, 3 ints), and Z is the return type (bool).

方法的参数一个接一个的列举在右边，中间没有分号。给出一个更复杂的例子：

method(I\[\[IILjava/lang/String;\[Ljava/lang/Object;)Ljava/lang/String;

在java语言中，他应该是这样的

String method(int, int\[\]\[\], int, String, Object\[\])

### 域

域同样的被指定为一个冗长的包括域，域名，域类型的形式。此外，它也允许虚拟机找到正确的域来表现字节编码的静态分析。

它们采用如下形式：

Lpackage/name/ObjectName;->FieldName:Ljava/lang/String;

这相当的不言自明---分别表示域名，域名和域类型

在dalvik字节码中，寄存器总是32位可以存放任何类型的值，2个寄存器可以用来存放64位类型（长整形和双精度型）

指定方法中的寄存器数字

这里有两种办法来指定一种方法中的多个寄存器。寄存器直接指定方法中的寄存器总数，或者区域变量直接指定方法中的非参数寄存器数。寄存器总数包括方法中参数所需的所有寄存器。

多少参数传入方法

当一个方法被调用，方法中的参数被放置到最近的几个寄存器。如果一个方法拥有两个参数和五个寄存器，这些参数会被存放到最后的两个寄存器V3和V4.

动态方法的第一个参数总是被调用的第一个对象，例如让你写一个动态方法 LMyObject;->callMe(II)V，这个方法有两个整形参数，但是还有一个隐含的参数‘LMyObject’在两个整形参数之前，所以总共有三个参数在这个方法中。

我们假设你在一个方法中制定了5个寄存器（v0-v4），既不是寄存器5管理也不是本地寄存器2管理（2个本地寄存器和3个参数寄存器）

当该方法被调用后，被调用的对象是V2，第一个整型参数是V3，第二个是V4。在静态方法中也是同样的，除非没有隐含的this指针参数。

寄存器名称

对于寄存器这里有两种命名方案—标准的V命名方案和对于参数寄存器的P命名方案。

以p命名的第一个寄存器就是方法中的第一个参数寄存器，所以让我们回到之前的总共拥有5个寄存器其中3个参数的例子。下表中显示了为每个寄存器的标准v命名，紧接着是为参数寄存器的p命名

v0     the first local register

第一个本地寄存器

v1     the second local register

第二个本地寄存器

v2  p0  the first parameter register

第一个参数寄存器

v3  p1  the second parameter register

第二个参数寄存器

v4  p2  the third parameter register

第三个参数寄存器

你可以通过每个名称类参照参数寄存器—这没有什么两样

p命名方案是作为一个实用的内容被引入的来解决编辑smali编码中的共同的难点。

假设你有一个已经存在的方法拥有一定数目的参数，你想增加一些代码到这个方法，你会发现你需要一个额外的寄存器。

你会想，没什么大不了的，我仅仅在寄存器管理中增加寄存器数目就可以了。但是不幸的是，这并不容易。请记住方法中的参数是寄存在最近的寄存器中的。如果你增加寄存器数—你会改变方法参数的在寄存器中的输入。所以你就不得不改变寄存器管理并且重编号参数寄存器

但是如果是你在方法中使用p命名模式来引用参数寄存器，你可以很容易的改变方法中的寄存器数而不必担心重编号任何存在的寄存器。

注意：默认baksmali对于参数寄存器使用p命名模式来命名。如果你因为某些原因不适用p命名而强制使用V命名模式，你可以使用-p/--no-parameter-registers来选择。

Long/Double values

长整型/双精度型值

鉴于以前提到过，长整型和双精度型（用J和D分别代表）的基本单元是64位，需要使用2个寄存器。

当你引用方法中的参数时必须牢牢记住。

例如，我们夹着你拥有一个非静态的方法LMyObject;->MyMethod(IJZ)V。方法的参数是“LMyObject;”，整形，长整形和布尔型。所以在这个方法中会要求五个寄存器来存放所有的参数。并且，当你以后调用这个方法时，你为了调用该指令不得不在寄存器列表中指定两个寄存器来存放任何64位的参数

Baksmali现在可以来反编译odex文件，并且选择性的deodex他们。

注意:本页说明仅仅应用于baksmali v0.96-v1.1。v1.2以上版本开始不再要求deodexrant.随心版本说明可以在http://code.google.com/p/smali/wiki/DeodexInstructions找到。

详细介绍

他要求deodexrant 的帮助，这是一个较小的可以在手机上运行并且连接到dalvik酷的二进制文件

他的目的在从正在运行的dalvik中获得类似寄存器表，域偏移量等信息提供给baksmali

Deodexrant的语法如下：

deodexerant <odex_file> <port>

接着baksmali拥有一个新的选项来告诉deodex文件，语法如下：

baksmali -x <host>:<port> <odex_file>

你也可以用-x :<port> 作为 –x localhots:<port>的快捷方式

主要的事情你必须记住的是当你将文件deodex化的时候，当odex文件被创建的时候你必须运行一些“bootclasspath”jar文件

最容易满足的这个的是当你尝试deodex的时候运行在硬件上。如果你需要从不同的硬件上deodex一些文件时也有可能改变根目录环境，但是我会将它作为一个练习留给读者

我们假设你正在运行一个基于美国移动商的官方版本的rom,并且苹果程序在/system/app目录下被deodex。一个deodex交互会像如下情形：

adb push deodexerant /data/local

adb shell chmod 755 /data/local/deodexerant

adb forward tcp:1234 tcp:1234

adb pull /system/app/Calculator.odex .

adb shell /data/local/deodexerant /system/app/Calculator.odex 1234 &

java -jar baksmali.jar -x :1234 Calculator.odex

你会活得一系列的smali文件在外层目录下，准备好用smali反编译这些dex文件。

注意我如何适应adb来通过usb来通信。如果你试着用wifi这个将会很慢。

鉴于baksmali向deodex制造了大量短小的同步请求,潜在上抑制了性能，所以usb连接比wifi用时更少

Troubleshooting

解决问题

When deodexing, there are several common issues you may run into.

当进行deodex的时候，可能会有几个问题你会遇上

The most common, which is almost guaranteed to happen if you're deodexing an entire build, is an error which is something like "Cannot find class Lfoo/bar; for common superclass lookup".

最常见保证很有可能发生的是如果你deodex一个完整的构造，会出现一个类似的错误“Cannot find class Lfoo/bar; for common superclass lookup”。

这是由于当odex文件拥有在标准的BOOTCLASSPATH一个额外的附属物超出了jars的范围

This is caused when the odex file has an additional dependency, beyond the jars in the normal BOOTCLASSPATH. To resolve the issue, you need to find which jar contains the class mentioned in the error message, and then add that to the BOOTCLASSPATH environment variable (on the phone/device/emulator) before running deodexerant.

为了解决这个问题，你需要找到那个包含错误信息提示的那个类的jar文件并且在运行deodexrant前将它添加到BOOTCLASSPATH环境变量中。

You can usually guess which jar it is from the class name, but if not, you can disassemble the jars and find which one has that class.

通常你可以根据类名来猜测是哪个jar，如果不行，你可以反编译jars然后找出包含该类的jar

Once you find the extra dependency, let's say /system/framework/com.google.android.maps.jar (which is one that is commonly needed), the deodexerant command would be

(on linux/mac)

一旦你找到了该额外的附属，我们就假设它是/system/framework/com.google.android.maps.jar

（这是普遍被需求的一个jar），那么deodexrant命令就如下（linux和mac平台）

adb shell BOOTCLASSPATH=\\$BOOTCLASSPATH:/system/framework/com.google.android.maps.jar deodexerant blah.odex 1234 &

Windows平台

adb shell BOOTCLASSPATH=$BOOTCLASSPATH:/system/framework/com.google.android.maps.jar deodexerant blah.odex 1234 &

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

另外的一件你可能遇到的事实堆溢出。错误提示类似“java.lang.OutOfMemoryError: Java heap space”。解决他的办法是你可以增加java参数-Xmx512m来增加堆到512Mb。当然如果需要你也可以增加到更大。如果你正在使用类似“java -jar baksmali.jar”来运行baksmali，可以用如下指令来代替

java -Xmx512m -jar baksmali.jar -x :1234 blah.odex

如果你正在使用封装脚本

baksmali -JXmx512m -x :1234 blah.odex

\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

另外一件事是发生在一些平台上（个人认为尤其是windows平台）的堆溢出。错误提示类似“java.lang.StackOverflowError”。错误的堆栈轨迹很可能是包含了类似"at org.jf.dexlib.Util.DeodexUtil$insn.propagateRegisters(DeodexUtil.java:1396)"的说明。

为了修正这个错误，你可以用-Xss10m指令来增加堆空间

java -Xss10m -jar baksmali.jar -x :1234 blah.odex

or

baksmali -JXss10m -x :1234 blah.odex