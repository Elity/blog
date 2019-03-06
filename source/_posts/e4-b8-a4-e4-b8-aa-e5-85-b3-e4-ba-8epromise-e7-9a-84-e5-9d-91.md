---
title: 两个关于Promise的坑
tags:
  - Promise
url: 2297.html
id: 2297
comments: false
categories:
  - 编程随想
date: 2018-06-28 15:55:47
---

### 1.Promise.race
[Promise.race](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/race#Description)用于处理一般竞态问题，参数为一个~~迭代器~~可迭代对象，该对象中的任意成员状态发生改变，`Promise.race`返回的`promise`状态随即发生改变。
然而，当该对象里面没有值的情况下，`Promise.race`返回的`promise`将永远处于`pending`状态。
`Promise.all`对于空的可迭代对象则永远返回一个`resolved`状态的`promise`。
此情此景类似于`Array.prototype.some`与`Array.prototype.every`:
`[].some(()=>{}) === false`
`[].every(()=>{}) === true`


### 2.thenable
[Promise.resolve](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve) 接受的参数可能为一个`promise`对象或其他对象，当这个对象不是`Promise`对象的实例时，若这个对象有then方法或其原型链上面有then方法，我们称这个对象为thenable对象，`Promise.resolve`内部会执行这个`then`方法，并传入`onFulfilled`与`onRejected`两个原生函数，用于调用决定返回的Promise实例的状态。
此策略用于将`jQuery.Deferred()`返回的对象及其他`类Promise`对象转成原生Promise，然而，此举导致我们在`resolve`一个值时需要格外小心，这个值的原型链上面是否有自定义的then方法，比如：
```javascript
Array.prototype.then = function(){}
const pro = Promise.resolve([1,23])
```
pro将永远处于`pending`状态。

要弄瘫一个现代化的网站（大量依托于Promise来实现异步逻辑），只需要一行代码：
```javascript
Object.prototype.then = function(){}
```
而且造成的错误原因还很不好查找根源。



PS：
1.若某对象有next方法，则其可能是迭代器
2.若某对象有[\[Symbol.iterator\]](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Iteration_protocols)方法，则其是可迭代对象

生成器对象既是迭代器，也是可迭代对象，其[Symbol.iterator]方法执行后返回其自身