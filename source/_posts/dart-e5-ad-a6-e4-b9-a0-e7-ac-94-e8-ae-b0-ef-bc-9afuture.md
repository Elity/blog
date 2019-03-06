---
title: Dart学习笔记：Future
tags:
  - dart
  - Future
  - 学习
url: 2462.html
id: 2462
comments: false
categories:
  - 编程随想
date: 2019-01-28 22:19:41
---

Dart下的`Future`类似于ES6下新增的`Promise`，也是为了解决异步回调带来的各种问题。

### 构造函数

> `Future(FutureOr<T> computation())`

computation 的返回值可以是普通值或者是`Future`对象

```dart
Future<num> future1 = Future((){
	print('async call1');
	return 123;
});

Future<Future> future2 = Future((){
	print('async call2');
	return future1;
});

```
需要注意的是，`computation`函数体中的代码是被异步执行的，这与`Promise`构造函数的回调执行时机是不一样的，如需要被同步执行，则使用如下这个命名构造函数：

> `Future.sync(FutureOr<T> computation())`

```dart
Future<num> future = Future.sync((){
	print('sync call');
	return 123;
});
```

> `Future.delayed(Duration duration, [ FutureOr<T> computation() ])`

延时后再执行computation
```dart
Future.delayed(Duration(seconds: 1), () {
	print("print after 1 second");
});
```

> `Future.value([FutureOr<T> value ])`

创建一个future对象，以value完成
```dart
Future.value(123);
```


> `Future.error(Object error, [ StackTrace stackTrace ])`

创建一个future对象，以错误状态完成
```dart
Future.error('some error');
```

> `Future.microtask(FutureOr<T> computation())`

dart下的异步任务队列有两个：`event queue`和`microtask queue`，`microtask queue`的优先级更高，而future的任务默认是属于`event queue`。上面这个构造函数就可以创建属于`microtask queue`的future。
```dart
Future.microtask(()=>print("microtask"));
```

### 实例方法

> `then<R>(FutureOr<R> onValue(T value), { Function onError }) → Future<R>`

行为与ES6中`Promise.prototype.then`几乎一致
```dart
Future.val(123).then((val)=>print(val));

Future.error("some error").then((val) {},{onError: (err) => print(err)});
```

> `catchError(Function onError, { bool test(Object error) }) → Future<T>`

处理future中的异常，第二个参数不使用的情况下，与ES6中的`Promise.prototype.catch`一致
```dart
Future.error("some error").catchError((err) => print(err));
```
第二个参数提供的情况下，必须返回true，回调函数才能正常捕获错误。暂没想到该参数有什么用。

> `asStream() → Stream<T>`

创建流，该流包含future中的值或错误

> `timeout(Duration timeLimit, { FutureOr<T> onTimeout() }) → Future<T>`

给future指定一个超时时间，若超时了执行`onTimeout`回调，并返回新的值作为下一个future的值

```dart
  Future.delayed(Duration(seconds: 2)).timeout(Duration(seconds: 1),
      onTimeout: () {
    print("timeout");
    return "TIME OUT";
  });
```

> `whenComplete(FutureOr action()) → Future<T>`

类似于ES7中的`Promise.prototype.finally`,无论future是正常完成还是产生异常都会执行
```dart
Future.value("value").whenComplete(() {
    print('complete');
  })
```


### 静态方法

> `any<T>(Iterable<Future<T>> futures) → Future<T>`

行为与ES6中`Promise.race`一致，若futures为空，则返回的future永远不会完成

```dart
Future.any([
  Future.delayed(Duration(seconds: 1)).then((val) => 1),
  Future.delayed(Duration(seconds: 2)).then((val) => 2),
  Future.delayed(Duration(seconds: 3)).then((val) => 3),
]).then((val) {
  print(val); // val == 1
});
```

> `doWhile(FutureOr<bool> action()) → Future`

do{}while()的异步版本
```dart
var i = 0;
Future.doWhile(() {
  print(i++);
  return Future.value(i < 10);
}).then((val) => print('after doWhile'));
```

> `forEach<T>(Iterable<T> elements, FutureOr action(T element)) → Future`

异步forEach，future会一直等待elements遍历完毕才会变成完成状态，除非在遍历过程中出现错误

```dart
Future.forEach([1, Future.delayed(Duration(seconds: 2)), 3], (val) {
  print(val);
  return val;
}).then((val) => print('success=>>>$val'));
```


> `wait<T>(Iterable<Future<T>> futures, { bool eagerError: false, void cleanUp(T successValue) }) → Future<List<T>>`

只`eagerError`参数未`true`的情况下，其行为与ES6中`Promise.all`基本一致
```dart
Future.wait([
  Future.delayed(Duration(seconds: 1)).then((val) => 1),
  Future.delayed(Duration(seconds: 2)).then((val) => 2),
  Future.delayed(Duration(seconds: 3)).then((val) => 3),
]).then((val) => print(val));  // 3s后得到val 为 [1,2,3]
```
`eagerError`默认值为`false`，表明futures中任何一项执行出错都不会立即返回新的future，而是需要等待所有项都是完成状态才会返回。

举例来讲，以下代码在`eagerError`为`false`的情况下，需要经历3s才会输出`err=>error`,而设置`eagerError`为`true`,则只需要1.5s:
```dart
Future.wait([
  Future.delayed(Duration(milliseconds: 1500))
      .then((val) => Future.error('error')),
  Future.delayed(Duration(seconds: 1)).then((val) => 1),
  Future.delayed(Duration(seconds: 2)).then((val) => 2),
  Future.delayed(Duration(seconds: 3)).then((val) => 3),
], eagerError: false)
    .then((val) => print('then=>$val'), onError: (err) => print('err=>$err'));
```
`cleanUp`在futures中某项出错的时候，会给每项正常执行的future提供清理操作，传递给`cleanUp`的参数为每个正常执行项的完成值，`Future.await`只会处理最先抛出的错误，但是整个程序会等待所有的future项完成才会结束。
```dart
Future.wait([
  Future.delayed(Duration(milliseconds: 1500))
      .then((val) => Future.error('error')),
  Future.delayed(Duration(seconds: 1)).then((val) => 1),
  Future.delayed(Duration(seconds: 2)).then((val) => 2),
], cleanUp: (val) => print('complete=>${val}'))
    .then((val) => print('then=>$val'), onError: (err) => print('err=>$err'));
```
以上代码会在1.5s后依次输出：
```
complete=>1
complete=>2
err=>error
```


---

粗略过完了所有的API，应该还有很多细节遗漏的地方，特别是在内部任务调度这块，涉及到代码执行顺序的问题，需要在实际项目中去摸索、加深。