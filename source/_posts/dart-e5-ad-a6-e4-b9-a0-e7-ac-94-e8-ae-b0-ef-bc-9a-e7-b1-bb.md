---
title: Dart学习笔记：类
tags:
  - dart
  - 学习
url: 2442.html
id: 2442
comments: false
categories:
  - 编程随想
date: 2019-01-26 23:43:27
---

本想按《Dart学习笔记 1: xxx》格式的，但这样就相当于立了flag，后面还需要有2、3、4...还是算了吧，指不定就烂尾了。
今天刚好又回过头来瞄了下[官方文档](https://www.dartlang.org/guides/language/language-tour#classes)，顺便记下笔记。

### 声明
```dart
class Point {
  num x; // 成员变量x，初始值null.
  num y = 0; // 成员变量y，初始化为0.
  num _z = 1; // 成员变量_z, 初始化为1，仅在库内可访问
}
```

### 实例化
```dart
Point p = new Point();
// dart2.0 可以省略 new关键字
Point p1 = Point();
```

### 构造函数
几种类型的构造函数：
1. 未定义构造函数的时候，会自动生成一个无参的默认构造函数，并且会调用超类的没有参数的构造函数。
2. 普通构造函数：定义一个和类名字一样的方法就定义了一个构造函数

```dart
// 常规形式
class Point {
  num x;
  num y;
  Point(num x, num y) {
    // 如果不存在同名冲突，this可以省略
    this.x = x;
    this.y = y;
  }
}
// 语法糖形式
class Point {
  num x;
  num y;
  Point(this.x, this.y);
}
```
3. 命名构造函数

> 使用命名构造函数可以为一个类实现多个构造函数， 或者使用命名构造函数来更清晰的表明你的意图。

```dart
class Point {
  num x;
  num y;
  Point.fromJson(Map json) {
    x = json[&#039;x&#039;];
    y = json[&#039;y&#039;];
  }
}
```
4. 常量构造函数

> 如果你的类提供一个状态不变的对象，你可以把这些对象 定义为编译时常量。要实现这个功能，需要定义一个 const 构造函数， 并且声明所有类的变量为 final。

```dart
class ImmutablePoint {
  final num x;
  final num y;
  const ImmutablePoint(this.x, this.y);
  static final ImmutablePoint origin =
      const ImmutablePoint(0, 0);
}
```
5. 工厂方法构造函数

> 如果一个构造函数并不总是返回一个新的对象，则使用 factory 来定义 这个构造函数。例如，一个工厂构造函数 可能从缓存中获取一个实例并返回，或者 返回一个子类型的实例。

```dart
class Logger {
  final String name;
  bool mute = false;

  // _cache 是库内私有
  static final Map&lt;String, Logger&gt; _cache =
      &lt;String, Logger&gt;{};

  factory Logger(String name) {
    if (_cache.containsKey(name)) {
      return _cache[name];
    } else {
      final logger = new Logger._internal(name);
      _cache[name] = logger;
      return logger;
    }
  }
  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) {
      print(msg);
    }
  }
}
// 使用的时候和其它构造函数一样
var logger = Logger(&#039;UI&#039;);
logger.log(&#039;Button clicked&#039;);
```


### 重定向构造函数

> 有时候一个构造函数会调动类中的其他构造函数。 一个重定向构造函数是没有代码的，在构造函数声明后，使用冒号调用其他构造函数。

```dart
class Point {
  num x;
  num y;
  Point(this.x, this.y);
  Point.alongXAxis(num x) : this(x, 0);
}
```

### 初始化列表

> 在构造函数体执行之前除了可以调用超类构造函数之外，还可以 初始化实例参数。 使用逗号分隔初始化表达式。

```dart
class Point {
  final num x;
  final num y;
  final num distanceFromOrigin;

  Point(x, y)
      : x = x,
        y = y,
        distanceFromOrigin = sqrt(x * x + y * y){
			print(&#039;In Point.fromJson(): ($x, $y)&#039;);
		}
}
```

### 调用超类构造函数

```dart
class Person {
  String firstName;
  Person.fromJson(Map data) {
    print(&#039;in Person&#039;);
  }
}

class Employee extends Person {
  // Person类没有默认构造函数，必须手动调用super.fromJson(data);
  Employee.fromJson(Map data) : super.fromJson(data) {
    print(&#039;in Employee&#039;);
  }
}
```

### 方法
```dart
import &#039;dart:math&#039;;

class Point {
  num x;
  num y;
  Point(this.x, this.y);

  num distanceTo(Point other) {
    var dx = x - other.x;
    var dy = y - other.y;
    return sqrt(dx * dx + dy * dy);
  }
}
```

### Getters and Setters
> Getters 和 Setters 是用来设置和访问对象属性的特殊函数。每个实例变量都隐含的具有一个 getter， 如果变量不是 final 的则还有一个 setter。 你可以通过实现 getter 和 setter 来创建新的属性， 使用 get 和 set 关键字定义 getter 和 setter。getter 和 setter 的好处是，我们在代码中一开始使用实例变量，后来，把实例变量用函数包裹起来，而使用实例变量的地方不需要修改。

```dart
class Rectangle {
  num left;
  num top;
  num width;
  num height;

  Rectangle(this.left, this.top, this.width, this.height);

  num get right             =&gt; left + width;
      set right(num value)  =&gt; left = value - width;
  num get bottom            =&gt; top + height;
      set bottom(num value) =&gt; top = value - height;
}
```

### 抽象类
> 使用 abstract 修饰符定义一个 抽象类—一个不能被实例化的类。 抽象类通常用来定义接口， 以及部分实现。抽象类通常具有抽象函数。

```dart
abstract class AbstractContainer {
  void updateChildren(); // 抽象方法
}
```

### 隐式接口
> dart已经移除了interface关键字，也就是说，在dart中不存在如java或ts中那样明确定义接口的语法，取而代之的是每个类都隐式的定义了一个包含所有实例成员的接口， 并且这个类自己实现了这些接口，当然，由于dart同时也不支持成员函数的`overload`，所以，实现接口的时候，函数签名必须与接口定义的一致

```dart
// A person. The implicit interface contains greet().
class Person {
  // In the interface, but visible only in this library.
  final _name;

  // Not in the interface, since this is a constructor.
  Person(this._name);

  // In the interface.
  String greet(String who) =&gt; &#039;Hello, $who. I am $_name.&#039;;
}

// Impostor必须实现Person的所有成员函数及变量.
class Impostor implements Person {
  // 尽管不会用到_name，也必须定义该成员变量
  get _name =&gt; &#039;&#039;;

  String greet(String who) =&gt; &#039;Hi $who. Do you know who I am?&#039;;
}

```
实现多个接口：

```dart
class Point implements Comparable, Location {...}
```

### 继承
> 继承就跟es6差不多了，使用`extends`关键字来定义子类，并可以在子类中通过`super`关键字来引用超类(所有祖宗类)

```dart
class Television {
  void turnOn() {
    _illuminateDisplay();
    _activateIrSensor();
  }
  // ···
}

class SmartTelevision extends Television {
  void turnOn() {
    super.turnOn();
    _bootNetworkInterface();
    _initializeMemory();
    _upgradeApps();
  }
  // ···
}
```


### 重写方法
> 可以使用 @override 注解来表明你的函数是想重写超类的一个函数

```dart
class SmartTelevision extends Television {
  @override
  void turnOn() {...}
  // ···
}
```

### 重写操作符
> [这些操作符](https://www.dartlang.org/guides/language/language-tour#overridable-operators)可以被覆写。 例如，如果你定义了一个 Vector 类， 你可以定义一个 + 函数来实现两个向量相加。

```dart
class Vector {
  final int x;
  final int y;
  const Vector(this.x, this.y);

  /// 覆写 + (a + b).
  Vector operator +(Vector v) {
    return new Vector(x + v.x, y + v.y);
  }

  /// 覆写 - (a - b).
  Vector operator -(Vector v) {
    return new Vector(x - v.x, y - v.y);
  }
}
```

### 类的Mixins
> “我们需要一种在多个类层次结构中重用类的代码的方法”，大白话就是当我们想要在不共享相同类层次结构的多个类之间共享行为时，或者在超类中实现此类行为没有意义时，Mixins非常有用。

先看定义mixin的方式：
1. 通过类隐式定义(不能有构造函数)

```dart
class Walker {
  void walk() {
    print(&quot;I&#039;m walking&quot;);
  }
}
```
2. 通过抽象类定义

```dart
abstract class Walker {
  // This class is intended to be used as a mixin, and should not be extended directly.
  factory Walker._() =&gt; null;
  void walk() {
    print(&quot;I&#039;m walking&quot;);
  }
}
```
3. 通过`mixin`关键字定义

```dart
mixin Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
  bool canConduct = false;

  void entertainMe() {
    if (canPlayPiano) {
      print(&#039;Playing piano&#039;);
    } else if (canConduct) {
      print(&#039;Waving hands&#039;);
    } else {
      print(&#039;Humming to self&#039;);
    }
  }
}
```
使用的时候，通过`with`关键字，后面跟一个或多个mixin

```dart
class Cat extends Mammal with Walker {}
class Dove extends Bird with Walker, Flyer {}
```

### 静态成员
> 使用`static`关键字来实现类级别的变量和函数。

```dart
class Queue {
  static const initialCapacity = 16;
  // ···
}

class Point {
  num x;
  num y;
  Point(this.x, this.y);
  // 由于静态方法不是通过实例访问，所以其内部无法使用this
  static num distanceBetween(Point a, Point b) {
    var dx = a.x - b.x;
    var dy = a.y - b.y;
    return sqrt(dx * dx + dy * dy);
  }
}
```

