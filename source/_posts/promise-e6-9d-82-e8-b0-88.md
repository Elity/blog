---
title: Promise杂谈
tags:
  - js
  - Promise
url: 2299.html
id: 2299
comments: false
categories:
  - 编程随想
date: 2018-06-29 20:42:08
---

> 最近又花时间理了一下Promise的设计逻辑，有一些小收获整理下。

控制反转(IOC)
---------

如果我们使用Spring(Java)或Laravel(PHP)这样的后端语言框架，我们不可避免的会与控制反转(IOC)、依赖注入(DI)这俩概念打交道，事实上，他们是同一个概念站在不同角度的描述。 我们有如下类，`Man`继承自`People`：

    class People{
        work(){}
    }
    
    class Man extends People{}
    

现有另一个`Work`类：

    class Work{
        constructor(){
            this.people = new Man()
        }
        doWork(){
            this.people.work()
        }
    }
    

这样，我们就把`Man`写死在了`Work`里面。 现在我们增加一个`Woman`,也继承自`People`:

    class Woman extends People{}
    

我们想让`Woman`工作的时候，要么就重新写一个类`Work1`，要么就只能把`Man`改为`Woman`，这显然不是一种好的实现。通过控制反转的思想，我们重构`Work`:

    class Work{
        constructor(people){
            if(people instanceof People){
                this.people = people
            }else{
                throw new TypeError('类Work构造函数应该传入类People的实例')
            }
        }
        doWork(){
            this.people.work()
        }
    }
    

然后，我们可以分别在`Work`中使用`Man`与`Woman`：

        const man = new Man()
        const woman = new Woman()
        new Work(man).work()
        new Work(woman).work()
    

显然，重构以后，使用`Man`与`Woman`的控制权交到了使用`Work`类的人手中，而不是创建`Work`类的人手中，这就是控制反转。 控制反转的情况，在Javascript代码经常打交道的回调函数处体现得淋漓尽致：

    function work(workFn){
        workFn && workFn()
    }
    
    work(function(){
        console.log('A')
    })
    
    work(function(){
        console.log('B')
    })
    

然而，控制反转被应用于回调这样的场景带来了诸多问题： 1\. work会不会真的调用workFn？ 2. work会调用几次workFn？ 3. work会同步还是异步调用workFn？ 4. 传入workFn的参数是否恒定？ 5. work内部出现错误或调用workFn出现错误如何处理？ 6. 无法组合多个回调逻辑（并联、串联） 出现这么多疑问，终究归到两个字：`信任`。由于`work`可能由其他小伙伴提供，甚至极端的可能由第三方提供，这种情况下，我们把回调函数交给`work`，内心是充满诸多不安的，我们无法信任`work`是否会如我们期望的方式执行。 而Promise的出现，反转了控制反转，很好的解决了代码的信任问题。

承诺
--

Promise即承诺，Promise的实现遵循Promise的相关规范(目前为[Promise/A+翻译版](https://malcolmyu.github.io/2015/06/12/Promises-A-Plus)，[原文](https://promisesaplus.com/)) Promise会在语法层面上确保promise状态发生改变时，会去调用用户传递给then方法的回调函数，并自动返回一个经`Promise.resolve`包装的promise，这也就是之所以Promise取名叫Promise的原因吧。换句话说，我们把控制反转反转了，但这个回调也不是由我们自己调用，而是交由一个可靠的系统（Promise）来调用，确保用户的回调函数执行情况是可以被信任的。 Promise只有三种状态：`Pending`、`Fulfilled`、`Rejected`，而且状态变化的方向只能是： `Pending->Fulfilled` 或 `Pending->Rejected` 一旦状态改变，就不会再次发生变化，确保了回调问题2、4，只会调用一次业务逻辑，且值是确定的。 状态改变的同时，Promise内部就会异步调用`Promise.prototype.then`的回调函数，要么成功，要么失败，此解决了回调问题3，promise的回调永远都是异步的 配合 Promise.race即可处理某些Promise一直处于`Pending`状况的问题，解决回调问题1 配合Promise.prototype.catch可以很方便的处理promise中的异常情况，解决回调问题5 配合Promise.all可以实现多个Promise的并联，再加上Promise的链式传递特性，解决回调问题6