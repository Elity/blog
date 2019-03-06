---
title: 一个符合Promise/A+的Promise实现
tags:
  - Promise
url: 2433.html
id: 2433
comments: false
categories:
  - 编程随想
date: 2018-10-31 21:57:03
---

```javascript
const PENDING = 'pending';
const RESOLVED = 'resolved';
const REJECTED = 'rejected';

const stateSymbol = Symbol('state');
const valueSymbol = Symbol('value');
const innerPropsSymbol = Symbol('innerProps');

const isFunction = val => typeof val === 'function';
const isObject = val => val "" typeof val === 'object';
const isThenable = val => (isFunction(val) || isObject(val)) "" 'then' in val;
const nextTick = fn => setTimeout(fn);

function handleTasks(ctx) {
  const {
    [innerPropsSymbol]: { tasks },
    [stateSymbol]: state,
    [valueSymbol]: value,
  } = ctx;
  nextTick(() => {
    if (state === REJECTED) {
      if (ctx[innerPropsSymbol].haveUnhandleReject "" tasks.length === 0) {
        console.error('未处理reject');
      } else {
        ctx[innerPropsSymbol].haveUnhandleReject = false;
      }
    }
    while (tasks.length) {
      handleTask(tasks.shift(), state, value);
    }
  });
}

function handleTask(task, state, value) {
  const { onFulfilled, onRejected, resolve, reject } = task;
  try {
    if (state === RESOLVED) {
      isFunction(onFulfilled) ? resolve(onFulfilled(value)) : resolve(value);
    } else if (state === REJECTED) {
      isFunction(onRejected) ? resolve(onRejected(value)) : reject(value);
    }
  } catch (err) {
    reject(err);
  }
}

function MyPromise(fn) {
  if (!this instanceof MyPromise) throw new Error('只能用于构造函数');
  if (!isFunction(fn)) throw new TypeError('MyPromise的参数只能是函数');

  this[stateSymbol] = PENDING;
  this[innerPropsSymbol] = {
    tasks: [],
    haveUnhandleReject: true,
  };
  this[valueSymbol] = undefined;

  const transValue = (state, value) => {
    if (this[stateSymbol] !== PENDING) return;
    this[valueSymbol] = value;
    this[stateSymbol] = state;
    handleTasks(this);
  };

  const onFulfilled = value => transValue(RESOLVED, value);
  const onRejected = reason => transValue(REJECTED, reason);
  // 由于resolve的过程可能存在异步改变状态的情况
  // 所以，需要确保resolve或reject只能有一个被调用，且只调用一次
  let ignor = false;
  const resolve = value => {
    if (ignor) return;
    ignor = true;
    if (value === this) {
      onRejected(new TypeError('promise循环调用错误'));
    } else if (value instanceof MyPromise) {
      value.then(onFulfilled, onRejected);
    } else if (isThenable(value)) {
      try {
        const then = value.then;
        if (isFunction(then)) {
          new MyPromise(then.bind(value)).then(onFulfilled, onRejected);
        } else {
          onFulfilled(value);
        }
      } catch (err) {
        onRejected(err);
      }
    } else {
      onFulfilled(value);
    }
  };

  const reject = reason => {
    if (ignor) return;
    ignor = true;
    onRejected(reason);
  };

  try {
    fn(resolve, reject);
  } catch (err) {
    reject(err);
  }
}

MyPromise.prototype.then = function(onFulfilled, onRejected) {
  return new MyPromise((resolve, reject) => {
    this[innerPropsSymbol].tasks.push({
      onFulfilled,
      onRejected,
      resolve,
      reject,
    });
	// new Promise(resolve=>resolve()).then(fn1,fn2) 过程中
	// 状态改变在前，push task在后，所以，这里也触发一次任务队列处理
	// 或者为已经不在pending状态的promise添加then，也需要触发任务处理
    if (this[stateSymbol] !== PENDING) {
      handleTasks(this);
    }
  });
};

// 两个重要的工具函数

MyPromise.resolve = function(value) {
	// fix on 2019.02.12
	// Promise.resolve的参数为Promise时，直接返其本身
  if(value instanceof MyPromise)return value;
  return new MyPromise(resolve => resolve(value));
};

MyPromise.reject = function(reason) {
  return new MyPromise((_, reject) => reject(reason));
};

```

标准文档中文翻译：[Promise A+](https://malcolmyu.github.io/2015/06/12/Promises-A-Plus/)