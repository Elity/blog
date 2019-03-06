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
Array.from({length:100},(v,k)=>k);
//or
Array.from(Array(100), (v,k)=>k);
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
                done: this.v > this.e
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
  if (i < 99) { yield* angry(i + 1); }
};
Array.from(angry(0));
```
⑦递归
```javascript
(function wallace(i) { return (i < 0) ? [] : wallace(i - 1).concat(i); })(99);
```
⑧尾递归
```javascript
(function mistake(i, acc) { return (i < 100) ? mistake(i + 1, acc.concat(i)) : acc; })(0, []);
```
⑨计时器都想到了
```javascript
var i = 0;
var a = [];
var timer = setInterval(function () {
  a[i] = i++;
  if (i >= 100) {
    clearInterval(timer);
    console.log(a);
  }
}, 1);
 
//or
 
var a = [];
var i = 0;
function step(timestamp) { 
  if (i < 100) {
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
new Array(101).join('a')
    .replace(/a/g, function (v, i) { return i + ','; })
    .split(',').splice(0, 100);
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
        return (i < 0) ? [] : excited(i - 1).concat(i);
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
    case 'jl':
      if (vm.ax < vm.bx) {
        vm.pc = ops[0]
        return vm.interpret()
      }
    case 'inc':
      vm.ax++
      break
    case 'ld':
      vm[ops[0]] = ops[1]
      break
    case 'push':
      vm.stack.push(vm[ops[0]])
      break
    case 'pop':
      vm[ops[0]] = vm.stack.pop()
      break
    case 'arr_push':
      vm.bx.push(vm.ax)
      break
    case 'ret':
      return vm.ax
      break
    default:
      throw 'wtf??'
  }
  vm.pc++
  return vm.interpret()
}
 
function make_a_100_len_array_without_fucking_loop() {
  var codes = [
    ['ld', 'ax', 0],  // 0
    ['ld', 'bx', []], // 1
    ['push', 'bx'],   // 2
    ['push', 'ax'],   // 3
    ['pop', 'ax'],    // 4
    ['pop', 'bx'],    // 5
    ['arr_push'],     // 6
    ['inc'],          // 7
    ['push', 'bx'],   // 8
    ['push', 'ax'],   // 9
    ['ld', 'bx', 100],// 10
    ['jl', 4],        // 11
    ['pop', 'bx'],    // 12
    ['pop', 'ax'],    // 13
    ['ret']
  ]
  var vm = new VM()
  return vm.run(codes)
}
 
var arr = make_a_100_len_array_without_fucking_loop();

```