---
title: javascript之throttle(节流)与debounce(防抖)
tags:
  - Javascript
  - 函数节流
  - 函数防抖
url: 2110.html
id: 2110
categories:
  - 编程随想
date: 2016-12-14 18:25:53
---

throttle适用于resize或者鼠标移动事件，防止浏览器频繁响应事件，严重拉低性能

    function throttle(method, delay) {
      var timer = null,
        last = 0,
        ctx,
        args,
        exec = function () {
          method.apply(ctx, args);
          last = +new Date;
        };
      return function () {
        var now = +new Date,
        diff = now - last - delay;
        ctx = this;
        args = arguments;
        clearTimeout(timer);
        if (diff >= 0) {
          exec()
        } else {
          timer = setTimeout(exec, -diff)
        }
      }
    }
    

debounce适用于诸如input事件，当用户输入时需要响应ajax请求，多次input只响应一次回调方法

    function debounce(method, delay) {
      var timer = null,
      ctx,
      args;
      return function () {
        ctx = this;
        args = arguments;
        clearTimeout(timer);
        timer = setTimeout(function () {
          method.apply(ctx, args);
        }, delay);
      }
    }