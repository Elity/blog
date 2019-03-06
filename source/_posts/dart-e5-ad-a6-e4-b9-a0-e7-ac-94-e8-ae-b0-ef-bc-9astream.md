---
title: Dart学习笔记：Iterable与Stream
url: 2471.html
id: 2471
comments: false
categories:
  - 软件物语
date: 2019-01-29 17:03:51
tags:
---

避免烂尾，先开坑。

初略看了下Stream API，应该是一个很深的坑。Stream实现了类似于Rx的许多API，估计过年在家要好好读读文档了。

---
本打算只写关于`Stream`的东西，后面翻了一下文档，发现`Stream`与`Iterable`的API大致相同，唯一的区别在于`Iterable`是同步的，而`Stream`是异步的，它们的定义形式，类似于JavaScript下的生成器，而实际上，在Dart中，也将二者统称为生成器。

- 定义`Iterable`
```dart
Iterable&lt;int&gt; naturalsTo(int n) sync* {
  int k = 0;
  while (k &lt; n) yield k++;
}
```
- 定义`Stream`
```dart
Stream&lt;int&gt; asynchronousNaturalsTo(int n) async* {
  int k = 0;
  while (k &lt; n) yield k++;
}
```
- 递归生成器
```dart
Iterable&lt;int&gt; naturalsDownFrom(int n) sync* {
  if (n &gt; 0) {
    yield n;
    yield* naturalsDownFrom(n - 1);
  }
}
```
