---
title: Dart学习笔记：异常
tags:
  - dart
  - 学习
url: 2455.html
id: 2455
comments: false
categories:
  - 编程随想
date: 2019-01-28 00:58:27
---

Dart 和 Java 不同的是，所有的 Dart 异常是非检查异常。 方法不声明他们可能抛出的异常， 并且你不被要求捕获任何异常。

Dart 提供了 `Exception` 和 `Error` 类型， 以及许多预定义的子类型。你还可以定义自己的异常类型。

### Throw

> Dart 不仅仅可以抛出`Exception`或者`Error `对象，还可以抛出任何非`null`对象为异常。

```dart
throw new FormatException(&#039;Expected at least 1 section&#039;);

throw &#039;Out of llamas!&#039;;
```

### Catch

> 捕获异常可以避免异常继续传递（你重新抛出异常除外）。 捕获异常给你一个处理该异常的机会。

```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  buyMoreLlamas();
}
```

> 对于可以抛出多种类型异常的代码，你可以指定多个捕获语句。每个语句分别对应一个异常类型， 如果捕获语句没有指定异常类型，则该可以捕获任何异常类型：

```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // A specific exception
  buyMoreLlamas();
// e为异常对象，s为调用堆栈信息
} on Exception catch (e, s) {
  // Anything else that is an exception
  print(&#039;Unknown exception: $e&#039;);
} catch (e) {
  // No specified type, handles all
  print(&#039;Something really unknown: $e&#039;);
}
```

> 使用 `rethrow` 关键字可以把捕获的异常重新抛出。

```dart
final foo = &#039;&#039;;

void misbehave() {
  try {
    foo = &quot;You can&#039;t change a final variable&#039;s value.&quot;;
  } catch (e) {
    print(&#039;misbehave() partially handled ${e.runtimeType}.&#039;);
    rethrow; // Allow callers to see the exception.
  }
}
```

### Finally

> 要确保某些代码执行，不管有没有出现异常都需要执行，可以使用 一个 `finally` 语句来实现。如果没有 `catch` 语句来捕获异常， 则在执行完 `finally` 语句后， 异常还是会被抛出。

```dart
try {
  breedMoreLlamas();
} finally {
  // Always clean up, even if an exception is thrown.
  cleanLlamaStalls();
}

try {
  breedMoreLlamas();
} catch(e) {
  print(&#039;Error: $e&#039;);  // Handle the exception first.
} finally {
  cleanLlamaStalls();  // Then clean up.
}
```
