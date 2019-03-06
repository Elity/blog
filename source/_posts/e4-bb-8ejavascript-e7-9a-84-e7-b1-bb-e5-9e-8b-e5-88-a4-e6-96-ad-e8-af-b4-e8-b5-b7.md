---
title: JavaScript的数据类型
tags:
  - Javascript
  - 类型
url: 2482.html
id: 2482
comments: false
categories:
  - 编程随想
date: 2019-02-11 15:58:41
---

## 变量名与变量值

```javascript
var age = 15;
```

变量名只是标识符，可以理解为内部指针，指向变量值所在的内存地址。变量名没有类型一说。

在一个姓名唯一的星球上，张三用于指代某个人，张三就是变量名，那个人就是变量值。当然，这个人还可以同时拥有多个其它名字：阿三、阿三哥、小三、小三三等。
由于无名之人会挂掉。所以，当把张三、阿三...这些名字都分配给其它人之后，这个人就没有存在的意义了，别人无法描述这个人，也就无法再次给他分配名字（[垃圾回收](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Memory_Management#%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6)机制）。

**后面我们依然会用这个设定来解释变量的引用。**

PS：以上仅仅提到了垃圾回收机制中最简单的一种，复杂的，比如：

```javascript
var o = {};
var arr = [];
arr.push(o);
o = undefined;
```

由于`arr`依然持有原来变量`o`指向的对象，所以就算标识符`o`不再引用该对象，但该对象依然被标记引用，不能被作为垃圾回收

## 类型

- 6 种基本类型
  - null
  - undefined
  - string
  - number
  - boolean
  - symbol
- 1 种复合类型
  - object

看到这里很多人就会发问了，那么我们用的数组、函数都算什么呢？

[![ky4eJg.gif](https://s2.ax1x.com/2019/02/17/ky4eJg.gif)](https://imgchr.com/i/ky4eJg)

又有机智的小伙伴会问了，那 JSON 又是什么？ [JSON\_互动百科](http://www.baike.com/wiki/json)

看很多人的简历写了：精通 JavaScript、JSON 等技术。我就一直在纳闷，JSON 有什么好精通的。

### 基本类型

基本类型直接代表了最底层的语言实现，具有如下特点：

- 不可变
- 无方法/属性

#### 不可变：

```javascript
var s = &#039;hello world&#039;;
console.log(s[1]); // 输出 e
s[1] = &#039;a&#039;;
console.log(s); // 依然是 hello world
```

扩展概念：[字符串驻留](http://en.wikipedia.org/wiki/String_interning)

可以通过[内存快照](https://developers.google.com/web/tools/chrome-devtools/memory-problems/heap-snapshots)的方式验证如下变量`a`和`b`是否指向同样的内存地址:

```javascript
var a = &#039;hello&#039;;
var b = &#039;hello&#039;;
```

#### 无方法/属性：

初看有些难以理解，毕竟我们经常这样用：

```javascript
&#039;Hello World&#039;.split(&#039; &#039;);
```

我们首先要搞明白两个事情：

- 上面的`split`方法就是`String.prototype.split`，验证：

```javascript
&#039;Hello World&#039;.split === String.prototype.split; // true
```

- 而 `"Hello World"` 并不是 `String` 的实例，验证：

```javascript
&#039;Hello World&#039; instanceof String; // false
```

上面这个例子要铭记，原始类型不能用`instanceof`判断。

那么为什么可以这样调用：`"Hello World".split(" ")`？

答案呼之欲出:

[百度:JavaScript 装箱](https://www.baidu.com/s?wd=JavaScript%20%E8%A3%85%E7%AE%B1&rsv_spt=1&rsv_iqid=0x9976ccb70006cf99&issp=1&f=8&rsv_bp=1&rsv_idx=2&ie=utf-8&rqlang=cn&tn=baiduhome_pg&rsv_enter=1&inputT=543&rsv_t=89520xyq7IZs9HTqFvSDlQfzFv3uYUDzrPJTG2YcAbFfM5EEvQpzOP%2BlxsvNpLpb92Wt&oq=JavaScript%2520%25E8%2587%25AA%25E5%258A%25A8%25E8%25A3%2585%25E7%25AE%25B1&rsv_pq=dccc221f00098ae5&rsv_n=2&rsv_sug3=56&rsv_sug2=0&rsv_sug4=544)

[Google:JavaScript boxing](https://www.google.com/search?newwindow=1&source=hp&ei=N61mXOP_Jsbj-Aa5h6WgAg&q=JavaScript+boxing&btnK=Google+Search&oq=JavaScript+boxing&gs_l=psy-ab.3..0i13j0i8i13i10i30j0i8i13i30j0i8i13i10i30j0i8i13i30l2.21077.24694..24738...1.0..0.131.1385.11j3......0....1..gws-wiz.....0..35i39j0i67j0i131j0j0i20i263.wUZG64TGqsM)

所以，在调用`"Hello World".split(" ")`时，实际调用的是`"Hello World"`对应的包装类`String`,实例化后的方法，等同于：

```javascript
new String(&#039;Hello World&#039;).split(&#039; &#039;);
```

如下几种基本类型：`string`、`number`、`boolean`、`symbol`都有对于的包装类，分别是[String](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/String)、[Number](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number)、[Boolean](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Boolean)、[Symbol](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol)

除了`Symbol`外，另外三个包装器类均可以被显示实例化，即`new String`、`new Number`、`new Boolean`均合法，但这并不好，而且任何时候都不鼓励显示创建包装器对象，这会模糊原始值与包装器对象的差异。所以在 ES6 中引入的`Symbol`不让被显示实例化([参考](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol#Description))：

```javascript
new Symbol(); // 报错： Symbol is not a constructor
```

但如果有别的原因，需要创建`symbol`的包装器对象，还是有方式的：

```javascript
var a = Object(Symbol());
typeof a; // object
```

### 复合类型

复合类型仅一种`object`

```javascript
typeof []; // object
typeof {}; // object
typeof Promise.resolve(); // object
typeof function() {}; // function    咦！我们中出了叛徒
```

莫慌，尽管在判断函数的时候返回了`function`，但就算烧成灰，在 JavaScript 中函数依然是属于`object`类型，一种在底层实现了`[[Call]]`内部方法的特殊`object`。

复合类型可变，可添加属性/方法，另外一大特点是按引用传值，怎么理解呢？看示例：

```javascript
var arr = [1, 2, 3];
var arr1 = arr;
arr1.push(4);
console.log(arr1); // [1,2,3,4]
console.log(arr); // [1,2,3,4]
```

发现异常没？明明我对`arr1`进行的操作，`arr`也跟着变了。我们这里不扯高深的内存操作，还是用那外星球的张三来做类比：

```javascript
var arr = [1, 2, 3]; // 某个人([1,2,3])一出生就被分配了名字：arr(张三)
var arr1 = arr; // 后面他又得到一个名字：阿三(arr1)
arr1.push(4); // 阿三(arr1)被人揍了一顿，毁了容，变成了 [1,2,3,4]
console.log(arr); // 你说张三毁容没毁容？
```

搞清数据类型的情况下，什么时候是按值传递，什么时候按引用传递是不是一目了然？

大声说出来，以下代码的输出结果：

```javascript
function fn() {}
fn.hehe = &#039;hehe&#039;;
console.log(fn.hehe); // 输出啥？
function factory(o) {
  o.hehe = &#039;haha&#039;;
}
factory(fn);
console.log(fn.hehe); // 输出啥？
```

另外，复合类型还有一大特性，不存在两个一样的值：

```javascript
{} === {} // false
[] === [] // flase
function a(){} === function a(){} // false
```

## 类型判断

- typeof
- instanceof
- Object.prototype.toString.call

### typeof

有 7 种数据类型，`typeof` 也返回了 7 个不同的类型值，然鹅这两个 7 并不一一对应：

```javascript
typeof 123 // number
typeof &#039;str&#039; // string
typeof true // boolean
typeof Symbol() // symbol
typeof undefined // undefined
typeof fucntion(){} // function
typeof {} // object
typeof null // object
typeof new String(&quot;hello&quot;) // object
```

`typeof null === 'object'`是一开始设计 JavaScript 的时候产生的[bug](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/typeof#null)，只能将错就错一直沿用下来，这是客户端语言的无奈（向下兼容）。

所以，当使用`typeof`判断一个数据是否为`object`时一定要排除`null`的情况：

```javascript
function isObject(obj) {
  return obj &amp;&amp; typeof obj === &#039;object&#039;;
}
```

至于`typeof fucntion(){}`返回`function`，是[标准](http://www.ecma-international.org/ecma-262/5.1/#sec-11.4.3)规定的。

另外另外另外，还有一种[特例](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/typeof#%E4%BE%8B%E5%A4%96)：

```javascript
typeof document.all === &#039;undefined&#039;;
```

至于原因，看看`document.all`与 IE 的[爱恨纠葛](https://www.cnblogs.com/ziyunfei/p/5618152.html)

所以，除了`null`，其他任意的数据我们均可以通过`typeof`操作符得到其类型。

至于，我们需要知道某对象具体是什么对象，比如：

- 我们怎么知道变量`arr`是否是`Array`的实例？
- 我们怎么知道变量`pro`是否是`Promise`的实例？

那么就要请出`instanceof`了

### instanceof

`instanceof` 顾名思义：`a instanceof A`的意义就是判断对象`a`是否是函数(也可以叫类)`A`的实例。

当然，以上表述不是完全准确，准确的表述应该是：判断`A.prototype`是否出现在了对象`a`的原型链上。

至于原型与原型链的概念及用途，看[这篇文章](./prototype-chain.md)。

下面简述`__proto__`的背景，方便代码演示，会在上面提到的文章中详细阐述。

> 最早由 Firefox 实现了通过属性`__proto__`访问对象的原型，这一特性如今几乎被所有的现代浏览器所实现，甚至还写入了 ES6 标准（尽管强烈不推荐在生产环境使用它，而是使用 Object.getPrototypeOf 方法），为方便，我们在演示代码中均用该特性读取原型，比如`a.__proto__`则是对象`a`的原型。

回到`instanceof`：

```javascript
var arr = [];
// 因为，以下表达式的结果为 true
arr.__proto__ === Array.prototype;
// 所以，以下表达式的结果也为 true
arr instanceof Array;

// 同样，由于以下表达式结果为 true
arr.__proto__.__proto__ === Object.prototype;
// 所以，以下表达式的结果也为 true
arr instanceof Object;
```

使用`instanceof`去判断对象的子类型有一些弊端，比如：

- [跨窗口的类型判断](<https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/instanceof#instanceof%E5%92%8C%E5%A4%9A%E5%85%A8%E5%B1%80%E5%AF%B9%E8%B1%A1(%E5%A4%9A%E4%B8%AAframe%E6%88%96%E5%A4%9A%E4%B8%AAwindow%E4%B9%8B%E9%97%B4%E7%9A%84%E4%BA%A4%E4%BA%92)>)

- 无原型的对象

```javascript
var o = Object.create(null);
o.__proto__; // undefined
typeof o; // object
o instanceof Object; // false
```

- 强行嫁接原型的情况

```javascript
var obj = {};
obj.__proto__ = Array.prototype;
obj instanceof Array; // true
```

- `instanceof`的行为可被自定义

```javascript
class MyClass {
  static [Symbol.hasInstance](instance) {
    return true;
  }
}

[] instanceof MyClass; // true

Object.create(null) instanceof MyClass; // true   完全不顾原型了

&#039;sss&#039; instanceof MyClass; // 连基本类型也不管了

var a;
// 理论上来讲，a 是任意值，下面表达式都为 true
a instanceof MyClass; // true
```

所以，除非你很确定的情况，不然不要轻易使用`instanceof`去判断一个值是否属于某个类型。

### Object.prototype.toString.call

人狠话不多，先看效果：

```javascript
Object.prototype.toString.call([]); // [object Array]
Object.prototype.toString.call({}); // [object Object]
Object.prototype.toString.call(&#039;&#039;); // [object String]
Object.prototype.toString.call(new Date()); // [object Date]
Object.prototype.toString.call(1); // [object Number]
Object.prototype.toString.call(function() {}); // [object Function]
Object.prototype.toString.call(/test/i); // [object RegExp]
Object.prototype.toString.call(true); // [object Boolean]
Object.prototype.toString.call(null); // [object Null]
Object.prototype.toString.call(); // [object Undefined]
```

这些形如："[object Array]"的值是从哪冒出来的呢？

依据[ES6 规范](https://www.ecma-international.org/ecma-262/6.0/#sec-object.prototype.tostring)中关于`Object.prototype.toString`的实现要求，我们翻译成伪代码就一目了然了：

```javascript
// 以下代码仅表示逻辑，不可作为js代码运行
function toString(val){
  if(val === undefined)return "[object Undefined]"
  if(val === null)return "[object Null]"
  let O = ToObject(val)
  let isArr = IsArray(val)
  ReturnIfAbrupt(isArr) // 如果 isArr 不是一个正常值，比如抛出一个错误，则中断执行
  let builtingTag;
  if(isArr) builtingTag = "Array";
  else if(O 是 exotic String object) builtingTag = "String";
  else if(O 有内部插槽 [[ParameterMap]]) builtingTag = "Arguments";
  else if(O 有内部方法 [[Call]]) builtingTag = "Function";
  else if(O 有内部插槽 [[ErrorData]]) builtingTag = "Error";
  else if(O 有内部插槽 [[BooleanData]]) builtingTag = "Boolean";
  else if(O 有内部插槽 [[NumberData]]) builtingTag = "Number";
  else if(O 有内部插槽 [[DateValue]]) builtingTag = "Date";
  else if(O 有内部插槽 [[RegExpMatcher]]) builtingTag = "RegExp";
  else builtingTag = "Object";
  let tag = O[Symbol.toStringTag];
  ReturnIfAbrupt(tag);
  if(Type(tag) 不是 String)tag = builtingTag
  return `[object ${tag}]`
}
```

上述伪代码用到了内部方法[ToObject](https://www.ecma-international.org/ecma-262/6.0/#sec-toobject)、[IsArray](https://www.ecma-international.org/ecma-262/6.0/#sec-isarray)、[Type](https://www.ecma-international.org/ecma-262/6.0/#sec-ecmascript-data-types-and-values)及[ReturnIfAbrupt](https://www.ecma-international.org/ecma-262/6.0/#sec-returnifabrupt)，内部类型[String Exotic Objects](https://www.ecma-international.org/ecma-262/6.0/#sec-string-exotic-objects)

特别说明下，很多书籍、博客里面提到的内部属性`[[Class]]`，在 ES6 规范中的`Object.prototype.toString`不再读取。

通过上面的伪代码，我们可以知道，用户可以控制`Object.prototype.toString`的返回值：

```javascript
let o = [];
Object.prototype.toString.call(o); // [object Array]
o[Symbol.toStringTag] = &#039;MyClass&#039;;
Object.prototype.toString.call(o); // [object MyClass]
```

至此，我们对三种判断类型的方式都已经做了介绍。

但是，你还需要搞清楚，类型判断的初衷是什么？

什么场景下使用什么方法你也需要仔细思量。

### 鸭子类型（duck typing）

[鸭子类型](https://zh.wikipedia.org/wiki/%E9%B8%AD%E5%AD%90%E7%B1%BB%E5%9E%8B)这个概念来自[鸭子测试](https://zh.wikipedia.org/wiki/%E9%B8%AD%E5%AD%90%E6%B5%8B%E8%AF%95)，当看到一只鸟走起来像鸭子、游泳起来像鸭子、叫起来也像鸭子，那么这只鸟就可以被称为鸭子。而在程序设计中：

> 鸭子类型（英语：duck typing）是动态类型的一种风格。在这种风格中，一个对象有效的语义，不是由继承自特定的类或实现特定的接口，而是由"当前方法和属性的集合"决定。在鸭子类型中，关注点在于对象的行为，能作什么；而不是关注对象所属的类型。

鸭子类型在原生的 JavaScript 中最显著的代表莫过于`Promise/A+`中的[thenable](https://promisesaplus.com/#the-promise-resolution-procedure)对象了。

`thenable`就是一个特殊的 js 对象，特殊在于这个对象本身或其原型链上存在`then`方法。所以，你懂的，当你在原型链的顶端添加了`then`方法，js 下任何的对象都成为了`thenable`对象：

```javascript
Object.prototype.then = function(resolve, reject) {};
```

如上的代码被添加以后，一个正常依赖`Promise`的项目就无法工作正常了。这是鸭子类型一直被诟病的地方。

但，凡事具有两面性，既然引入这个概念，而且在各大编程语言特别是动态语言中作为一种设计模式，具有不可替代的作用。

依然拿`thenable`来说，在`Promise`正式进入规范之前，已经有很多库实现了类似思想，比如[Q](https://github.com/kriskowal/q)、[jQuery.Deferred](https://api.jquery.com/category/deferred-object/)，如何保证已经使用了这些库的既有项目如何快速过度到标准的`Promise`呢？噢！感谢上帝，这些类库有一个共同特点，就是它们的对象都有`then`方法，在实现`Promise`时只需要这样判断一个值是否是`thenable`：

```javascript
var isThenable = obj =&gt;
  (typeof obj === &#039;function&#039; || (obj &amp;&amp; typeof obj === &#039;object&#039;)) &amp;&amp;
  &#039;then&#039; in obj;
```

如果用判断鸭子类型的方式来判断一个数组，我们可能需要这样判断：

```javascript
var obj = {};
var isArr = false;
if(&#039;splice&#039; in obj &amp;&amp; &#039;push&#039; in obj &amp;&amp; &#039;pop&#039; in obj....){
  isArr = true;
}
```

更有甚者，我们可能还需要判断`push`或`pop`操作以后，数组的长度是否自动发生了变化...一切的判断取决于你的需求。

你需要知道一个值是否如你所期望的那样，你得去察其外貌观其行为，如果都符合你的预期，你就把它当你所需类型使用，这就是鸭子类型。

无论是使用 js 提供的类型判断，还是使用鸭子类型去判断，在动态语言下面都不存在绝对的靠谱，`动态`提供了便利的同时，对于开发人员的素质要求也会更高。多人协作的项目、中大型项目、频繁迭代的项目，也许拥抱强类型语言是更稳妥的选择。

个人觉得，从语言层面上来讲，鸭子类型比较像是强类型语言里面的接口(interface)，接口的约束性更强，实现一个接口必须实现接口的所有方法与属性，甚至方法的签名都需要一模一样，减少了很多的不确定性。

下面以 typescript 的`接口`做演示结束本文：

```typescript
interface Animal {
  name: string;
  say();
}

class Cat implements Animal {
  name = '';
  constructor(name: string) {
    this.name = name;
  }
  say() {
    console.log(`我是猫，我叫${this.name}`);
  }
}

class People implements Animal {
  name = '';
  constructor(name: string) {
    this.name = name;
  }
  say() {
    console.log(`我是人，我叫${this.name}`);
  }
}

function factory(animal: Animal) {
  animal.say();
}

const tom = new Cat('tom');
const zs = new People('张三');

factory(tom);
factory(zs);

// 我是猫，我叫tom
// 我是人，我叫张三
```