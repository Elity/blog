---
title: 微信笔试题：LazyMan实现
tags:
  - js
  - Promise
  - 面试
url: 2420.html
id: 2420
comments: false
categories:
  - 编程随想
date: 2018-10-12 17:02:16
---

实现一个函数LazyMan，执行情况如下：

    LazyMan("Hank")
    // 输出:Hi! This is Hank!
     
    LazyMan("Hank").sleep(10).eat("dinner")
    // 输出 Hi! This is Hank!
    // 等待10秒..
    // 输出 Wake up after 10
    // 输出 Eat dinner
     
    LazyMan("Hank").eat("dinner").eat("supper")
    // 输出 Hi This is Hank!
    // 输出 Eat dinner
    // 输出 Eat supper
     
    LazyMan("Hank").sleepFirst(5).eat("supper")
    // 等待5秒
    // 输出 Hi This is Hank!
    // 输出 Eat supper
    

在有Promise的情况下，还是很好做的：

    function LazyMan(name) {
      function sleep(time) {
        return new Promise(resolve => setTimeout(resolve, time * 1000));
      }
    
      function addToQueue(queue, item, addToHead) {
        queue[addToHead ? 'unshift' : 'push'](item);
      }
    
      function resolvePromiseQueue(queue) {
        queue.reduce((last, cur) => {
          return last.then(cur);
        }, Promise.resolve());
      }
    
      const QUEUE_NAME = Symbol('QueueName'); // 通过Symbol ,使queue成为“私有”成员
    
      return new class LazyMan {
        constructor() {
          this[QUEUE_NAME] = [() => console.log(`Hi! This is ${name}!`)];
          Promise.resolve().then(resolvePromiseQueue.bind(null, this[QUEUE_NAME]));
        }
        eat(food) {
          addToQueue(this[QUEUE_NAME], () => console.log(`Eat ${food}`));
          return this;
        }
        sleep(time, sleepFirst) {
          addToQueue(
            this[QUEUE_NAME],
            () => sleep(time).then(() => console.log(`Wake up after ${time}`)),
            sleepFirst
          );
          return this;
        }
        sleepFirst(time) {
          this.sleep(time, true);
          return this;
        }
      }();
    }