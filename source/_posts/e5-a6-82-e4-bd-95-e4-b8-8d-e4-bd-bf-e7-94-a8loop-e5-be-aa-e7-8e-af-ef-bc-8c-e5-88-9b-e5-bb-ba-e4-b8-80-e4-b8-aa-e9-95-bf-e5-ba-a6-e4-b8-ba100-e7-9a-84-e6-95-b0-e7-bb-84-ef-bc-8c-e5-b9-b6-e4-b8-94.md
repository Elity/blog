---
title: 如何不使用loop循环，创建一个长度为100的数组，并且每个元素的值等于它的下标
tags:
  - ES6
  - Javascript
url: 2085.html
id: 2085
categories:
  - 编程随想
date: 2016-08-18 00:29:04
---

[知乎](https://www.zhihu.com/question/41493194)下一个提问炸出一堆见所未见的答案，当然，是我学识浅薄了。

①
```javascript
Object.keys(Array.apply(null,{length:100}));
//or
Object.keys(new Int8Array(100));
//or   
Object.keys(String(Array(101)));
```

②
```javascript
Array.from({length:100},(v,k)=&gt;k);
//or
Array.from(Array(100), (v,k)=&gt;k);
```

③
```javascript
Array.from(Array(100).keys());
```
④
```javascript
[...Array(100).keys()];
```
⑤ 这个略微惊叹：
```javascript
Number.prototype[Symbol.iterator] = function() {
    return {
        v: 0,
        e: this,
        next() {
            return {
                value: this.v++,
                done: this.v &gt; this.e
            }
        }
    }
}
[...100];
```

⑥生成器+递归
```javascript
function* angry(i){
  yield i;
  if (i &lt; 99) { yield* angry(i + 1); }
};
Array.from(angry(0));
```
⑦递归
```javascript
(function wallace(i) { return (i &lt; 0) ? [] : wallace(i - 1).concat(i); })(99);
```
⑧尾递归
```javascript
(function mistake(i, acc) { return (i &lt; 100) ? mistake(i + 1, acc.concat(i)) : acc; })(0, []);
```
⑨计时器都想到了
```javascript
var i = 0;
var a = [];
var timer = setInterval(function () {
  a[i] = i++;
  if (i &gt;= 100) {
    clearInterval(timer);
    console.log(a);
  }
}, 1);
 
//or
 
var a = [];
var i = 0;
function step(timestamp) { 
  if (i &lt; 100) {
    a[i] = i++;
    requestAnimationFrame(step);
  } else {
    console.log(a);
  }
}
requestAnimationFrame(step);
```
⑩
```javascript
new Array(101).join(&#039;a&#039;)
    .replace(/a/g, function (v, i) { return i + &#039;,&#039;; })
    .split(&#039;,&#039;).splice(0, 100);
```
⑾ Y组合子
```javascript
(function (excited) {
    return function (f) {
        return f(f);
    }(function (f) {
        return excited(function (x) { return (f(f))(x); });
    });
})(function (excited) {
    return function (i) {
        return (i &lt; 0) ? [] : excited(i - 1).concat(i);
    }
})(99);
```
最后是**大杀器**

```javascript
 
function VM() {
  this.stack = []
  this.cs = []
  this.pc = 0
  this.ax = undefined
  this.bx = undefined
}
VM.prototype.run = function(codes) {
  this.cs = codes
  this.pc = 0
  return this.interpret()
}
VM.prototype.interpret = function() {
  var vm = this
  var instruction = vm.cs[vm.pc]
  var op = instruction[0]
  var ops = instruction.slice(1)
 
  switch (op) {
    case &#039;jl&#039;:
      if (vm.ax &lt; vm.bx) {
        vm.pc = ops[0]
        return vm.interpret()
      }
    case &#039;inc&#039;:
      vm.ax++
      break
    case &#039;ld&#039;:
      vm[ops[0]] = ops[1]
      break
    case &#039;push&#039;:
      vm.stack.push(vm[ops[0]])
      break
    case &#039;pop&#039;:
      vm[ops[0]] = vm.stack.pop()
      break
    case &#039;arr_push&#039;:
      vm.bx.push(vm.ax)
      break
    case &#039;ret&#039;:
      return vm.ax
      break
    default:
      throw &#039;wtf??&#039;
  }
  vm.pc++
  return vm.interpret()
}
 
function make_a_100_len_array_without_fucking_loop() {
  var codes = [
    [&#039;ld&#039;, &#039;ax&#039;, 0],  // 0
    [&#039;ld&#039;, &#039;bx&#039;, []], // 1
    [&#039;push&#039;, &#039;bx&#039;],   // 2
    [&#039;push&#039;, &#039;ax&#039;],   // 3
    [&#039;pop&#039;, &#039;ax&#039;],    // 4
    [&#039;pop&#039;, &#039;bx&#039;],    // 5
    [&#039;arr_push&#039;],     // 6
    [&#039;inc&#039;],          // 7
    [&#039;push&#039;, &#039;bx&#039;],   // 8
    [&#039;push&#039;, &#039;ax&#039;],   // 9
    [&#039;ld&#039;, &#039;bx&#039;, 100],// 10
    [&#039;jl&#039;, 4],        // 11
    [&#039;pop&#039;, &#039;bx&#039;],    // 12
    [&#039;pop&#039;, &#039;ax&#039;],    // 13
    [&#039;ret&#039;]
  ]
  var vm = new VM()
  return vm.run(codes)
}
 
var arr = make_a_100_len_array_without_fucking_loop();

```