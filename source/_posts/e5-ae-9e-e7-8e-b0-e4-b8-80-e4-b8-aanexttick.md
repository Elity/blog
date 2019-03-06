---
title: 来一个更好的nextTick
tags:
  - nextTick
  - Promise
  - setTimeout
  - 异步
url: 2429.html
id: 2429
comments: false
categories:
  - 编程随想
date: 2018-10-26 20:17:25
---

这几天利用空余时间在完成一个去年未尽的活：从零开始实现一个Promise。 原生的Promise，then里面的回调被放入[事件循环](/2378.html "事件循环")中的微任务队列，而setTimeout的回调则被放入了宏任务队列。一轮事件循环结束，先清空微任务队列，才会执行一个宏任务。所以，Promise中then的回调执行时机早于setTimeou的回调执行时机的。 我们既然打算自己实现一个Promise的话，那么它的执行时机越早越好，从目前我所知的各种回调来说，没有什么比Promise更早了，那么存不存在一个比setTimeout执行时机更早的呢？ 好在，原来看[司徒正美的博客](https://www.cnblogs.com/rubylouvre/ "司徒正美的博客")有留意到他提过某些标签的onerror事件执行会比较早，具体记不得是哪篇博客了，于是乎自己尝试一番得到了下面这个函数：

    function nextTick(fn){
        var img = document.createElement('img');
        img.onerror = function(){fn()}
        img.src='data:;,'
    }
    

测试：

    setTimeout(function(){console.log(1)})
    nextTick(function(){console.log(2)});
    
    

在IE9+及其他现代浏览器上得到的输出顺序都是：2、1 创建一个Dom元素而不插入文档流，代价应该不是很大，如果实在要纠结这个，我们不如把它放入一个闭包中，达到重复利用：

    var nextTick = (function(){
        var img = document.createElement('img');
        return function nextTick(fn){
            img.onerror = function(){fn()}
            img.src='data:;,'
        }
    })()
    
    

另外，在IE10、IE11及Edge上有一个非标准的函数：`setImmediate`，[Vue](https://github.com/vuejs/vue/blob/52719ccab8fccffbdf497b96d3731dc86f04c1ce/src/core/util/next-tick.js#L37 "Vue")中的nextTick实现也会优先使用它，但我实验发现，它与`setTimeou`的执行顺序是无法确定。 另外另外另外再提一句，MutationObserver的回调和Promise一样，也属于micro-task，而且他的执行时机更是早于Promise。以下是实现：

    function  nextTick(fn){
        var div = document.createElement('div');
        new MutationObserver(function(){fn()}).observe(div, {  attributes: true })
        div.setAttribute('data-test','test')
    }