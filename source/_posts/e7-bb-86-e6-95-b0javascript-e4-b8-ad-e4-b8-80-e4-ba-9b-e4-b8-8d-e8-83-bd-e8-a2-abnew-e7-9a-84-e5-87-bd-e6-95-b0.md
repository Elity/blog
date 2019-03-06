---
title: 细数JavaScript中一些不能被new的函数
tags:
  - Javascript
  - 对象
url: 2473.html
id: 2473
comments: false
categories:
  - 编程随想
date: 2019-01-31 00:52:38
---

最近发现组内的很多人基础太不牢固，打算整一个关于JavaScript的思维导图，搞一次技术分享。结果发现原来自己也遗漏了相当多得细节。接下来，我就来细数下那些不能被new的函数，这些函数被new的时候会抛出错误：

    Uncaught TypeError: xxx is not a constructor
    

在js中，`function`类型是一种特殊的对象，特殊在何处呢？特殊在于它是一个在引擎内部实现了`[[Call]]`方法的对象，所以，在使用`typeof`取值的时候被特殊对待，返回了`function`。 对于`new`操作符也是一样，会去查看该对象内部是否实现了`[[Construct]]`方法，未实现则抛出上述的`TypeError`。

    var obj = {
        a(){},
        b: ()=>{},
        c: function* (){},
        d: async function d(){},
        e: async function* e(){},
        f: function f(){}
    }
    

以上列出的这些函数中仅仅`obj.f`可以被实例化。 另外，内置对象上的一些静态方法大多(未验证全部)不可实例化，如：`Math.pow`、`JSON.parse`等。 内置对象的一些实例方法也是同样，如：`[].splice`、`"hello".split`等。 内置的一些函数也是无法被实例化的，如：`encodeURI`、`Symbol`等。 最后还有`Function.prototype`，这个特殊的函数也不可被实例化。 ps: 以上提及的这些不能被实例化的函数有一个功能特点（除了Symbol）,那就是它们都没有`prototype`属性，然鹅，并不能说明一个类能不能被实例化与这个构造函数有没有`prototype`属性有必然联系，比如：

    var A = (function test(){}).bind(this);
    

bind得到的函数`A`就不存在`prototype`属性，但`A`依然可以被实例化。所以，还是开头的那个结论，一个函数能不能被实例化，就看其内部是否有实现`[[Construct]]`方法。 当然，话说回来，`prototype`属性决定了一个可被实例化的函数能否作为父类被继承。还是看这个代码：

    var A = (function test(){}).bind(this);
    class B extends A{}   // 报错 Uncaught TypeError: Class extends value does not have valid prototype property undefined
    
    A.prototype = null;
    class C extends A{}  // 正常
    

当当当然，null又是个特例，ES6规范规定了，跟在extends后面的只能是一个可被实例化且其`prototype`属性为对象(包括函数)或`null`的对象(包括函数)，所以下面的代码是合法的：

    class D extends null{}
    

但是这样声明的`D`无法被实例化，因为找不到超类的构造函数，需要这样处理：

    class D extends null{
        constructor(){
            return Object.create(new.target.prototype)
        }
        otherMethod(){}
    }