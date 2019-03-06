---
title: Vue中异步错误处理
tags:
  - vue
  - 异步，异步错误
url: 2213.html
id: 2213
comments: false
categories:
  - 编程随想
date: 2018-03-14 10:15:40
---

一般在一个项目开始之前，我们一般会对现有的框架做一定功能上的丰富，比如对ajax请求功能的二次封装，封装的功能可能包含了：通用错误处理，请求过滤，响应过滤等等。如果我们封装的函数叫request，那么业务中触发一个ajax请求的流程大致如图： ![正常ajax请求流程图](http://ww1.sinaimg.cn/large/0060lm7Tly1fpc3uu4pp5j30p90qbmyc.jpg) 通常，这样的流程处理能满足需求，然而，更多的情况，我们希望request的返回数据，经过request预处理后，首先交由业务代码这边自行判断是否合法，是否需要处理错误，如果不合法，且自己不打算处理错误，则再抛出错误，这样的话，就符合很多后端框架的流程了，业务层的错误，先自己catch，要么处理，要么往框架抛，框架统一处理，流程大致如图： ![改造后的request请求图](http://ww2.sinaimg.cn/large/0060lm7Tly1fpc4gonkflj30p90qbmya.jpg) 这样的话，所有的错误，业务层有了优先处理的权利，不需要自行处理的情况才交由框架做通用处理。 当然，在javascript中，异步错误的处理不能简简单单地通过`window.onerror`可以搞定的，在vue下改造，具体的思路是将我们需要按新的流程处理的函数处理成async函数，这样，我们就能用promise的那套错误处理机制来处理了。 具体实现看代码：

    //为了不影响vue自有的Vue.config.errorHandler正常工作
    //我们再单独定义一个异步错误处理函数
    Vue.config.asyncErrorHandler = err => {
      console.log('catch async error:', err)
    }
    
    

    //然后是在vue实例创建之前，包裹所有method方法
    //根据method执行后返回的是否是promise，来决定是否需要catch错误
    Vue.mixin({
      beforeCreate() {
        const methods = this.$options.methods || {}
        Object.keys(methods).forEach(key => {
          let fn = methods[key]
          this.$options.methods[key] = function(...args) {
            let ret = fn.apply(this, args);
            if (ret && typeof ret.catch === 'function') {
              return ret.catch(Vue.config.asyncErrorHandler)
            } else {
              return ret
            }
          }
        })
      },
    })
    
    

这样的话，我们在新建vue实例的时候，将我们需要按新流程处理错误的方法声明为async：

    new Vue({
      el: '#test',
      methods: {
        async fn() {
          this.name = 'click'
          await timeout()  // <==== timeout中reject的错误会被捕获
        },
        async fn1() {
          this.name = 'click1'
          throw {msg:'async函数中同步抛错',status:1000} // <==== 同步抛错也会被捕获
        },
        test() {
          // 原来的方式书写method，人为触发不捕获，
          // 生命周期中调用被Vue.config.errorHandler捕获
          throw {
            msg: '非async方法还是按照原来的方式处理错误',
            status: 3000
          }
        }
      }
    })
    
    

测试代码：[https://jsfiddle.net/a408115319/od5fgL4v/28/](https://jsfiddle.net/a408115319/od5fgL4v/28/) 封装了一个vue插件 ：[https://github.com/Elity/VueGlobalError](https://github.com/Elity/VueGlobalError)