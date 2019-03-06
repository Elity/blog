---
title: 对javascript继承的理解
tags:
  - Class
  - Javascript
  - 继承
url: 2064.html
id: 2064
categories:
  - 编程随想
date: 2016-03-19 00:24:00
---

昨天读了关于javascript继承的[几篇文章](http://www.cnblogs.com/sanshi/archive/2009/07/08/1519036.html)，对原型链、construct、this有了新的认识。

由于javascript原生是不支持类的(ES6已经支持class与extends)，更不用谈继承、多态了，为了模拟出一些其它面向对象编程语言的这些特性，有好多大牛写了给出了实现方式，看了John Resig的[《Simple JavaScript Inheritance》](http://ejohn.org/blog/simple-javascript-inheritance/)这篇文章，深深被折服了。原来短短几十行javascript也可以这么强大、优雅。

下面以我的理解方式来解读下。主要实现了继承、访问父类的重名方法（这里的实现方式太妙了），但遗憾的是不能实现成员变量/函数的隐藏。
```javascript
(function(){
  //设置标志位，是new A()过程中还是 B=A.extends({/*   */})过程中;
  var initializing = false, 
      //fnTest 可取结果为俩正则对象 /\b_super\b/与 /.*/ 
      //当正则的test方法参数支持自动调用toString()方法时取前面那个
      fnTest = /xyz/.test(function(){xyz;}) ? /\b_super\b/ : /.*/;
   
  // 创建一个全局的 Class对象
  this.Class = function(){};
   
  // 创建继承函数
  Class.extend = function(prop) {
    //此处把_super指向父类的prototype，属性继承时需要用其判断父、子是否有同名方法
    var _super = this.prototype;
     
    //开始B=A.extends({/*   */})；
    initializing = true;
    //实例化父类，并把父类的实例方法及属性给prototype
    var prototype = new this();
    initializing = false;
    //结束B=A.extends({/*   */})；
     
    // 遍历用户传入的用于构建子类的对象
    //处理的地方包括：
    //1、属性直接存到prototype上
    //2、方法中没通过this._super()调用父类中的同名方法，则直接把该方法存到prototype上
    //3、方法中有通过this._super()调用父类中的同名方法，则把如下过程包裹成一个函数存到prototype上:
    //   把this._super指向父类的同名方法，然后再调用子类的该方法并返回执行结果
    for (var name in prop) {
      // 循环体中看起来略微复杂，是整个代码的精华所在
      //简化下此过程就是  v = a &amp;&amp; b &amp;&amp; c ? d : e; 等同于 v = (a &amp;&amp; b &amp;&amp; c) ? d : e
      //该过程中主要运用的就是逻辑运算中的短路运算 a,b,c全为true则v=d，否则v=e
      //a中判断prop[name]是否是函数，b中判断父类中是否也有同名的name函数，c主要判断name函数中是否有调用父类的同名方法（即调用了this._super()）
      //d就是a,b,c同时满足的时候，也就是说：name是函数，且name函数存在与父类中，且子类的name函数需要调用了父类的同名函数
      //若a,b,c中有一项不满足则直接把prop[name]给prototype[name]
      prototype[name] = typeof prop[name] == &quot;function&quot; &amp;&amp;
        typeof _super[name] == &quot;function&quot; &amp;&amp; fnTest.test(prop[name]) ?
        (function(name, fn){
          //首先，先决条件决定了prototype[name]是个函数，所以先包裹一个函数返回
          return function() {
            //作者这里备份，然后调用fn后又还原this._super
            //由于return的是一个function,具有延时调用的作用 所以在子类调用fn时this始终指向子类本身
            //而这里的_super是父类的_super,与this._super并无关系，所以备份应该是多余的
            //var tmp = this._super;
            //this._super指向与fn(即prop[name])同名的父类方法,方便fn内部调用
            //这里是实现子类中通过this._super()调用父类同名函数的关键
            this._super = _super[name];
            //此时再调用子类，子类里面的this._super已经指向了父类同名的函数，即_super[name]
            var ret = fn.apply(this, arguments);    
            //this._super = tmp;
            //返回执行结果
            return ret;
          };
        })(name, prop[name]) :
        prop[name];
    }
     
    //此“Class”与外头那个“Class”是两个东西
    //这个Class实为子类的构造函数
    function Class() {
      //不是A.extends({/*   */})过程中
      if ( !initializing &amp;&amp; this.init )
        this.init.apply(this, arguments);
    }
     
    //前面处理好的prototype绑定给子类的prototype
    Class.prototype = prototype;
     
    //修正构造函数
    Class.prototype.constructor = Class;
   
    //赋予子类可被继续继承的功能
    Class.extend = arguments.callee;
     
    return Class;
  };
})();
```