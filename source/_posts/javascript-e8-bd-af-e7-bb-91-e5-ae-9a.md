---
title: javascript软绑定
tags:
  - bind
  - Javascript
  - 软绑定
url: 2205.html
id: 2205
comments: false
categories:
  - 编程随想
date: 2017-12-22 17:50:10
---

说道软绑定，那么不得不首先提到什么是硬绑定，如Function.prototype.bind，就是硬绑定，有如下代码：
```javascript
let obj1 = {
    id:'obj1'
},
obj2 = {
    id: 'obj2'
};

function print(){
    console.log(this.id)
}
let p1 = print.bind(obj1);
p1() // 输出 obj1
let p2 = p1.bind(obj2);
p2() // 依然输出 obj1 

```
这就是硬绑定的演示。一个函数一旦使用bind绑定过上下文，那么之后再绑定就不会生效了。
我这里要实现的软绑定，则就是为了某些特别需求而实现可以重复绑定的bind

```javascript
Function.prototype.softBind = function(ctx,...cur){
  let fn = this
  function ret(...arg){
	return ret.fn.apply(ctx,[...ret.arg,...arg])
  }
  ret.fn = this.fn || this
  // 不要忽视对参数的绑定
  ret.arg = [...(this.arg || []),...cur]
  return ret
}
```
但这种实现方式会对外暴露fn和arg，存在被污染的可能性