---
title: 为pyquery添加nextUntil方法
url: 2017.html
id: 2017
categories:
  - 编程随想
date: 2015-05-20 00:59:44
tags:
---

找到pyquery.py文件，在PyQuery类下添加如下代码：

    @with_camel_case_alias                #装饰器，兼容驼峰式命名调用
    def next_until(self, selector=None):
        all = self._next_all()        #获取匹配到的所有元素
        results = []
        for e in all:
            if self.__class__(e).is_(selector):   #到了指定选择器则break
                break
            results.append(e)
        return self.__class__(results, **dict(parent=self)) #将list通过自身构造函数转换成pyquery对象
    

还有，原谅我的无知，用了这么久的python，刚刚才知道函数内部引用全局变量并不需要在内部声明为global，当需要对全局变量进行修改时才需要