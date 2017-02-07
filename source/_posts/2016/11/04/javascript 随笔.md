---
title: javascript 随笔
date: 2016-11-04
categories: javascript
---

### 前言

本篇主要记录 javascript 中的一些特性，文章逻辑和排版比较随意，当然包含的内容，有很大的局限性，仅当草稿。


### 随笔

#### 严格模式 (use strict)

除了正常运行模式，ECMAScript 5 添加了第二种运行模式：“严格模式”（strict mode）。顾名思义，这种模式使得 JavaScript 在更严格的条件下运行。

设立”严格模式“的目的，主要有以下几个：

- 消除 JavaScript 语法的一些不合理、不严谨之处，减少一些怪异行为;
- 增加更多报错的场合，消除代码运行的一些不安全之处，保证代码运行的安全；
- 提高编译器效率，增加运行速度；
- 为未来新版本的 JavaScript 做好铺垫；

“严格模式”体现了 JavaScript 更合理、更安全、更严谨的发展方向。更多介绍可移步阅读 [阮一峰 - 严格模式](http://javascript.ruanyifeng.com/advanced/strict.html)

#### 包装对象

在 JavaScript 中，"一切皆对象"，数组和函数本质上都是对象，就连三种原始类型的值：数值、字符串、布尔值在一定条件下，也会自动转为对象，也就是原始类型的"包装对象"。

所谓"包装对象"，就是分别与数值、字符串、布尔值相对应的 `Number` 、 `String` 、 `Boolean` 三个原生对象。这三个原生对象可以把原始类型的值变成（包装成）对象。

```js
var v1 = new Number(123);
var v2 = new String("abc");
var v3 = new Boolean(true);

typeof v1  // "object"
typeof v2  // "object"
typeof v3  // "object"

v1 === 123  // false
v2 === "abc"  // false
v3 === true  // false
```

```js
var v = new String("abc");
v.length // 3

"abc".length // 3
```

上面代码对字符串 abc 调用 length 属性，实际上是将字符串自动转为 String 对象的实例，再在其上调用 length 属性。这就叫原始类型的自动转换。

abc 是一个字符串，属于原始类型，本身不能调用任何方法和属性。但当对 abc 调用 length 属性时，JavaScript 引擎自动将 abc 转化为一个包装对象实例，然后再对这个实例调用 length 属性，在得到返回值后，再自动销毁这个临时生成的包装对象实例。

```js
'abc'.charAt === String.prototype.charAt   // true
```

注意：如果直接对原始类型的变量添加属性，是无效。

```js
var s = "abc";
s.p = 123;
s.p  // undefined
```


#### 标记语句 (labeled statement)

通常和 break 或 continue 语句一起使用。标记就是在一条语句前面加个可以引用的标识符，任何不是保留关键字的 javascript 标识符。

```js
var i, j;

loop1:
for (i = 0; i < 3; i++) {
   loop2:
   for (j = 0; j < 3; j++) {
      if (i == 1 && j == 1) {
         continue loop1;
      }
      console.log("i = " + i + ", j = " + j);
   }
}
```

```js
var i, j;

loop1:
for (i = 0; i < 3; i++) {
   loop2:
   for (j = 0; j < 3; j++) {
      if (i == 1 && j == 1) {
         break loop1;
      }
      console.log("i = " + i + ", j = " + j);
   }
}
```

可参阅 [labeled statement](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/label)


#### 判断 NaN 的方法

isNaN 方法可以用来判断一个值是否为 NaN，isNaN 只对数值有效，如果传入其他值，会被先转成数值。

```js
isNaN(NaN) // true
isNaN(123) // false
isNaN('Hello') // true
isNaN({}) // true
isNaN(['xzy']) // true
isNaN([]) // false
isNaN([123]) // false
isNaN([123,456]) // true
isNaN(['123']) // false
```

因此判断 NaN 时要注意数据类型的转换。也可以自己封装一个 isNaN 函数。

```js
function myIsNaN(value) {
  return typeof value === 'number' && isNaN(value);
}
```

利用了 NaN 不等于自身的特性

```js
function myIsNaN(value) {
  return value !== value;
}
```

注意：如果数组中包含 NaN 下面的方法不适用]

```js
[NaN].indexOf(NaN) // -1
[NaN].lastIndexOf(NaN) // -1
```

这是因为这两个方法内部，使用严格相等运算符（===）进行比较，而 NaN 是唯一一个不等于自身的值。


#### 字符处理

在 javascript 中处理字符需要注意一些特殊字符，在这里紧给出简单例子。

```js
var s = '\ud834\udf06';
console.log(s);  // 𝌆
console.log(s.length);  // 2
console.log(/^.$/.test(s));  // false
```

特殊字符 `𝌆` length 属性为 2 ，匹配单个字符的正则表达式会失败。具体原因可[点击查阅](http://javascript.ruanyifeng.com/grammar/string.html#toc5)


#### 数组本质

数组属于一种特殊的对象。

```js
var arr = ['a', 'b', 'c'];
Object.keys(arr);  // ['0', '1', '2']
```

`Object.keys` 方法返回数组的所有键名。

还需要要注意数组的空位，即 `[,1,,3]` 这类型数组遍历的时候需要注意，利用 `for ... in` 结构会跳过空位。如果读取数组的空位，则返回 `undefined`。

空位和 `undefined` 是不一样的，如果数字某个位置是 `undefined` 遍历时不会被跳过。

```js
Object.keys([,1,,3]);  // ['1', '3']
```


#### 运算符

1. 且运算符 (&&)
且运算符的运算规则是：如果第一个运算子的布尔值为true，则返回第二个运算子的值（注意是值，不是布尔值）；如果第一个运算子的布尔值为false，则直接返回第一个运算子的值，且不再对第二个运算子求值。

```js
"t" && "" // ""
"t" && "f" // "f"
"t" && (1+2) // 3
"" && "f" // ""
"" && "" // ""

var x = 1;
(1-1) && (x+=1) // 0
x // 1
```

```js
if (i !== 0 ){
  doSomething();
}

// 等价于

i && doSomething();
```

```js
true && 'foo' && '' && 4 && 'foo' && true
```

2. 或运算符 (||)
或运算符的运算规则是：如果第一个运算子的布尔值为true，则返回第一个运算子的值，且不再对第二个运算子求值；如果第一个运算子的布尔值为false，则返回第二个运算子的值。

你可以[点击阅读](http://javascript.ruanyifeng.com/grammar/operator.html)有关运算符的知识，如位运算符，void 运算符的一些用法。


#### Math 对象

Math 对象是 JavaScript 的内置对象，提供一系列数学常数和数学方法。该对象不是构造函数，所以不能生成实例，所有的属性和方法都必须在 Math 对象上调用。

```js
new Math()  // TypeError: Math is not a constructor
```


#### JSON 对象

有关 JSON 对象的详细介绍可点击阅读: <http://javascript.ruanyifeng.com/stdlib/json.html> 其中像 `JSON.stringify()` `JSON.parse()` 的方法有多个参数。


#### RegExp 对象

有关 JavaScript 的正则可点击阅读: <http://javascript.ruanyifeng.com/stdlib/regexp.html>


#### new 命令

`new` 命令的作用，就是执行构造函数，返回一个实例对象。一般构造函数首字母大写。注意：应该避免出现不使用 new 命令、直接调用构造函数的情况，原因如下：

```js
var Vehicle = function (){
  this.price = 1000;
};

var v = Vehicle();
v.price  // TypeError: Cannot read property 'price' of undefined(…)

price // 1000
```

上面代码中，调用 Vehicle 构造函数时，忘了加上 new 命令。结果 price 属性变成了全局变量，而变量 v 变成了 undefined

为了保证构造函数必须与 new 命令一起使用，一个解决办法是，在构造函数内部使用严格模式，即第一行加上 `use strict`

```js
function Fubar(foo, bar){
  "use strict";

  this._foo = foo;
  this._bar = bar;
}

Fubar()  // ypeError: Cannot set property '_foo' of undefined(…)
```

上面代码的 Fubar 为构造函数，`use strict` 命令保证了该函数在严格模式下运行。由于在严格模式中，函数内部的 this 不能指向全局对象，默认等于 undefined ，导致不加 new 调用会报错。( JavaScript 不允许对 undefined 添加属性)

另一个解决办法，是在构造函数内部判断是否使用 new 命令，如果发现没有使用，则直接返回一个实例对象。

```js
function Fubar(foo, bar){
  if (!(this instanceof Fubar)) {
    return new Fubar(foo, bar);
  }

  this._foo = foo;
  this._bar = bar;
}

Fubar(1, 2)._foo // 1
(new Fubar(1, 2))._foo // 1
```

上面代码中的构造函数，不管加不加 new 命令，都会得到同样的结果。
更详细可阅读 <http://javascript.ruanyifeng.com/oop/basic.html#toc2>


#### Function.prototype.call

这里紧对 `Function.prototype.call` 做个简单的介绍，有关更详细的介绍可以阅读 [Annotated ECMAScript 5.1 ](http://es5.github.io/#x15.3.4.4) 语言规范里的介绍,在此之前先看看下面代码

```js
var test = function(){
  console.log('hello world')
  return 'fsjohnhuang'
}
test.call() // A
Function.prototype.call(test) // B
Function.prototype.call.call(test) // C
Function.prototype.call.call(Function.prototype.call, test) // D
```

运行结果： `A C D` 控制台显示 hello world 并返回 fsjohnhuang `B` 返回 undefined 且不会调用 test 函数。这是怎么回事？

以下是参照规范的伪代码

```js
Function.prototype.call = function(thisArg, arg1, arg2, ...) {

  // this 指向调用 call 的那个对象或函数
  // 如 Function.prototype.call(test) 那么这时 this 就是 Function.prototype

  // 1. 调用内部的 IsCallable(this) 检查是否可调用，返回 false 则抛 TypeError
  if (![[IsCallable]](this)) throw new TypeError()

  // 2. 创建一个空列表
  // 3. 将 arg1 及后面的入参保存到 argList 中
  var argList = [].slice.call(arguments, 1)

  // 4. 调用内部的[[Call]]函数
  return [[Call]](this, thisArg, argList)
}
```

明白了这层关系之后，就比较容易的推导上面例子的运行的一个流程了。再看一下两个例子

```js
var slice = Function.prototype.call.bind(Array.prototype.slice);

slice([1, 2, 3], 0, 1) // [1]
```

```js
Array.prototype.resolve = function(){
　　this.forEach(Function.prototype.call, Function.prototype.call)
}
var cbs = [function(){console.log(1)}, function(){console.log(2)}]
cbs.resolve() // 1 2
```

可以参考 [JS魔法堂：再次认识 Function.prototype.call](http://www.cnblogs.com/fsjohnhuang/p/4160942.html)


#### instanceof 运算符深入剖析

在此之前先看看 instanceof 常规用法

```js
function Foo(){}
var foo = new Foo();
console.log(foo instanceof Foo)  // true


function Aoo(){}
function Foo(){}
Foo.prototype = new Aoo();

var foo = new Foo();
console.log(foo instanceof Foo)  // true
console.log(foo instanceof Aoo)  // true
```

instanceof 复杂用法

```js
console.log(Object instanceof Object);  // true
console.log(Function instanceof Function);  // true
console.log(Number instanceof Number);  // false
console.log(String instanceof String);  // false

console.log(Function instanceof Object);  // true

console.log(Foo instanceof Function);  // true
console.log(Foo instanceof Foo);  // false
```

要理解上面代码的执行结果，需要从两个方面着手：语言规范中是如何定义这个运算符的 和 JavaScript 原型继承机制。

规范中 instanceof 运算符定义,这里给出伪代码

```js
function instance_of(L, R) {  // L 表示左表达式, R 表示右表达式
  var O = R.prototype;  // 取 R 的显示原型
  L = L.__proto__;  // 取 L 的隐式原型
  while (true) {
    if (L === null)
      return false;
    if (O === L)  // 这里重点：当 O 严格等于 L 时，返回 true
      return true;
    L = L.__proto__;
  }
 }
```

JavaScript 原型继承机制：在原型继承结构里面，规范中用 `[[Prototype]]` 表示对象隐式的原型，在 JavaScript 中用 __proto__ 表示。所有 JavaScript 对象都有 `__proto__` 属性，但只有 `Object.prototype.__proto__` 为 `null` 这个属性指向它的原型对象，即构造函数的 prototype
属性。

原型继承图

![Alt text](/myimages/2016-11-04_01.jpg "JavaScript 原型链")

下面简要分析下 `console.log(Foo instanceof Foo)`

```js
// 为了方便表述，首先区分左侧表达式和右侧表达式
 FooL = Foo, FooR = Foo;

 // 下面根据规范逐步推演
 O = FooR.prototype = Foo.prototype
 L = FooL.__proto__ = Function.prototype

 // 第一次判断
 O != L

 // 循环再次查找 L 是否还有 __proto__
 L = Function.prototype.__proto__ = Object.prototype

 // 第二次判断
 O != L

 // 再次循环查找 L 是否还有 __proto__
 L = Object.prototype.__proto__ = null

 // 第三次判断
 L == null

 // 返回 false
```

再看看下面例子的运行结果，帮助理解

```js
Function.prototype === Function.__proto__  // true
Number.prototype === Number.__proto__  // false
Number.__proto__ === Object.__proto__  // true
String.__proto__ === Object.__proto__  // true
```

相关文章可查阅 [JavaScript instanceof 运算符深入剖析](http://www.ibm.com/developerworks/cn/web/1306_jiangjj_jsinstanceof/) 和 [JavaScript 继承](http://javascript.ruanyifeng.com/oop/inheritance.html)


#### Event 对象

事件发生以后，会生成一个事件对象，作为参数传给监听函数。浏览器原生提供一个 Event 对象，所有的事件都是这个对象的实例，或者说继承了 Event.prototype 对象。

Event 对象本身就是一个构造函数，可以用来生成新的实例。

```js
event = new Event(typeArg, eventInit);
```

Event 构造函数接受两个参数。第一个参数是字符串，表示事件的名称；第二个参数是一个对象，表示事件对象的配置。该参数可以有以下两个属性。

- bubbles：布尔值，可选，默认为 false ，表示事件对象是否冒泡。
- cancelable：布尔值，可选，默认为 false ，表示事件是否可以被取消。

##### 自定义事件和事件模拟

除了浏览器预定义的那些事件，用户还可以自定义事件，然后手动触发。

```js
// 新建事件实例
var event = new Event('build');

// 添加监听函数
elem.addEventListener('build', function (e) { ... }, false);

// 触发事件
elem.dispatchEvent(event);
```

上面代码触发了自定义事件，该事件会层层向上冒泡。在冒泡过程中，如果有一个元素定义了该事件的监听函数，该监听函数就会触发。

Event 构造函数只能指定事件名，不能在事件上绑定数据。如果需要在触发事件的同时，传入指定的数据，需要使用 `CustomEvent` 构造函数生成自定义的事件对象。

```js
var myEvent = new CustomEvent("myevent", {
  detail: {
    foo: "bar"
  },
  bubbles: true,
  cancelable: false
});

el.addEventListener('myevent', function(event) {
  console.log('Hello ' + event.detail.foo);
});

el.dispatchEvent(myEvent);
```

由于 IE 不支持上述的两个 API ，如果在 IE 中自定义事件，需要其他写法，详细可阅读 [阮一峰 - 自定义事件和事件模拟](http://javascript.ruanyifeng.com/dom/event.html#toc52)

注意： [addEventListener](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener) 方法的第二个参数是实现 [EventListener](https://developer.mozilla.org/zh-CN/docs/Web/API/EventListener) 接口的一个对象或函数，具体应用可以看 [iscroll](https://github.com/cubiq/iscroll)

##### 事件的传播

当一个事件发生以后，它会在不同的 DOM 节点之间传播(propagation)。这种传播分成三个阶段：

- 第一阶段：从 window 对象传导到目标节点，称为“捕获阶段”（capture phase）。
- 第二阶段：在目标节点上触发，称为“目标阶段”（target phase）。
- 第三阶段：从目标节点传导回window对象，称为“冒泡阶段”（bubbling phase）。

假设 html 结构如下

```
<div id="div"><p id="p">content</p></div>
```

```js
var oDiv = document.getElementById('div');
var oP   = document.getElementById('p');

var myEvent = new CustomEvent("myevent", {
  detail: {
    foo: "bar"
  },
  bubbles: true,
  cancelable: false
});

var phases = {
  1: 'capture',
  2: 'target',
  3: 'bubble'
};

var callback = function callback(event) {
                  var tag = event.currentTarget.tagName;
                  var phase = phases[event.eventPhase];
                  console.log("Tag: '" + tag + "'. EventPhase: '" + phase + "'");
               }

oDiv.addEventListener('myevent', callback, true);
oDiv.addEventListener('myevent', callback, false);

oP.addEventListener('myevent', callback, true);
oP.addEventListener('myevent', callback, false);

// oDiv.dispatchEvent(myEvent);
oP.dispatchEvent(myEvent);


// 运行结果
// Tag: 'DIV'. EventPhase: 'capture'
// Tag: 'P'. EventPhase: 'target'
// Tag: 'P'. EventPhase: 'target'
// Tag: 'DIV'. EventPhase: 'bubble'
```

上面代码采用自定义事件的方式，可以看出 myevent 事件被触发了四次： p 节点的捕获阶段和冒泡阶段各 1 次， div 节点的捕获阶段和冒泡阶段各 1 次。

1. 捕获阶段：事件从 div 向 p 传播时，触发 div 的 click 事件；
2. 目标阶段：事件从 div 到达 p 时，触发 p 的 click 事件；
3. 目标阶段：事件离开 p 时，触发 p 的 click 事件；
4. 冒泡阶段：事件从 p 传回 div 时，再次触发 div 的 click 事件。

注意：用户点击网页的时候，浏览器总是假定 click 事件的目标节点，就是点击位置的嵌套最深的那个节点。

事件传播的最上层对象是 window ，接着依次是 document ，html 和 body 。也就是说，如果 body 元素中有一个 div 元素，点击该元素。事件的传播顺序，在捕获阶段依次为 window、 document、 html、 body、 div ，在冒泡阶段依次为 div、 body、 html、 document、 window

这里只是简要的说明了有关事件的部分知识点，相关更多的介绍如：节点的操作，CSS 操作等 DOM 的相关知识可详细阅读 [阮一峰 - DOM](http://javascript.ruanyifeng.com/#toc4)

PS:低版本IE不支持事件捕获


#### 定时器

setTimeout 和 setInterval 的运行机制是，将指定的代码移出本次执行，等到下一轮 Event Loop 时，再检查是否到了指定时间。如果到了，就执行对应的代码；如果不到，就等到再下一轮 Event Loop 时重新判断。这意味着， setTimeout 指定的代码，必须等到本次执行的所有代码都执行完，才会执行。

每一轮 Event Loop 时，都会将“任务队列”中需要执行的任务，一次执行完。 setTimeout 和 setInterval 都是把任务添加到“任务队列”的尾部。因此，它们实际上要等到当前脚本的所有同步任务执行完，然后再等到本次 Event Loop 的“任务队列”的所有任务执行完，才会开始执行。由于前面的任务到底需要多少时间执行完，是不确定的，所以没有办法保证， setTimeout 和 setInterval 指定的任务，一定会按照预定时间执行。

可以运行下面例子看看：

```js
setTimeout(function() {
  console.log("Timeout");
}, 0);

function a(x) {
  console.log("a() 开始运行");
  b(x);
  console.log("a() 结束运行");
}

function b(y) {
  console.log("b() 开始运行");
  console.log("传入的值为" + y);
  console.log("b() 结束运行");
}

console.log("当前任务开始");
a(42);
console.log("当前任务结束");

// 当前任务开始
// a() 开始运行
// b() 开始运行
// 传入的值为42
// b() 结束运行
// a() 结束运行
// 当前任务结束
// Timeout
```

应用：用户在输入框输入文本转换大写英文字母，假设页面上有一元素 `<input type="text" id="input-box">` 脚本如下：

```js
document.getElementById('input-box').onkeypress = function(event) {
   this.value = this.value.toUpperCase();
}
```

```js
document.getElementById('input-box').onkeypress = function() {
  var self = this;
  setTimeout(function() {
    self.value = self.value.toUpperCase();
  }, 0);
}
```

可分别测试上面代码，看看异同。具体可参考阅读以下两篇文章：
[阮一峰 - 浏览器的JavaScript引擎](http://javascript.ruanyifeng.com/bom/engine.html)
[阮一峰 - 定时器](http://javascript.ruanyifeng.com/bom/timer.html)

有关于 Event Loop 的介绍可以阅读以下文章：
[nodejs事件轮询详述](http://www.cnblogs.com/xiaozhi_5638/p/4816265.html)
[并发模型与Event Loop](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/EventLoop)
[什么是 Event Loop？](http://www.ruanyifeng.com/blog/2013/10/event_loop.html)
[理解Node.js的event loop](https://cnodejs.org/topic/50462f51329c5139760bff98)


#### JavaScript 的异步处理

JavaScript 语言的执行环境是”单线程”（single thread）。所谓”单线程”，就是指一次只能完成一件任务。如果有多个任务，就必须排队，前面一个任务完成，再执行后面一个任务。

这种模式的好处是实现起来比较简单，执行环境相对单纯；坏处是只要有一个任务耗时很长，后面的任务都必须排队等着，会拖延整个程序的执行。常见的浏览器无响应（假死），往往就是因为某一段 Javascript 代码长时间运行（比如死循环），导致整个页面卡在这个地方，其他任务无法执行。下文简要介绍在 JavaScript 中异步的处理方式。


##### 定时器方式

利用 `setTimeout` 最简单的一种和常用的异步处理方式

```js
setTimeout(function(){
   // ...
}, 0);
```


##### 回调函数

```js
function fn(callback){
    // ...
    if(true){
        callback();
    }
}
```

回调函数的优点是简单、容易理解和部署，缺点是不利于代码的阅读和维护，各个部分之间高度耦合，使得程序结构混乱、流程难以追踪（尤其是回调函数嵌套的情况），而且每个任务只能指定一个回调函数。


##### 事件监听

这种方法的优点是比较容易理解，可以绑定多个事件，每个事件可以指定多个回调函数。缺点是整个程序都要变成事件驱动型，运行流程会变得很不清晰。简单的例子就像 jQuery 绑定自定义事件然后达到一定条件后主动触发。


##### 发布/订阅模式

该模式又叫观察者模式,它定义了对象间的一种一对多的依赖关系，以便一个对象的状态发生变化时，所有依赖于它的对象都得到通知并自动刷新。下面给出一个 [pubsubz](https://github.com/addyosmani/pubsubz) 的源码。

```js
;(function ( window, doc, undef ) {

    var topics = {},
        subUid = -1,
        pubsubz ={};

    // 发布
    pubsubz.publish = function ( topic, args ) {

        if (!topics[topic]) {
            return false;
        }

        setTimeout(function () {
            var subscribers = topics[topic],
                len = subscribers ? subscribers.length : 0;

            while (len--) {
                subscribers[len].func(topic, args);
            }
        }, 0);

        return true;

    };

    // 订阅
    pubsubz.subscribe = function ( topic, func ) {

        if (!topics[topic]) {
            topics[topic] = [];
        }

        var token = (++subUid).toString();
        topics[topic].push({
            token: token,
            func: func
        });
        return token;
    };

    // 退订
    pubsubz.unsubscribe = function ( token ) {
        for (var m in topics) {
            if (topics[m]) {
                for (var i = 0, j = topics[m].length; i < j; i++) {
                    if (topics[m][i].token === token) {
                        topics[m].splice(i, 1);
                        return token;
                    }
                }
            }
        }
        return false;
    };

    getPubSubz = function(){
        return pubsubz;
    };

    window.pubsubz = getPubSubz();

}( this, this.document ));
```

使用方法

```js
var testSubscriber = function( topics , data ){
    console.log( topics + ": " + data );
};

var testSubscription = pubsubz.subscribe( 'example1', testSubscriber );

pubsubz.publish( 'example1', 'hello world!' );
pubsubz.publish( 'example1', ['test','a','b','c'] );
pubsubz.publish( 'example1', [{'color':'blue'},{'text':'hello'}] );

setTimeout(function(){
    pubsubz.unsubscribe( testSubscription );
}, 0);

pubsubz.publish( 'example1', 'hello again!' );
```

从源码可以看出，内部主要实现了 发布，订阅，退订 这三个方法。观察者的使用场合就是：当一个对象的改变需要同时改变其它对象，并且它不知道具体有多少对象需要改变的时候，就应该考虑使用观察者模式。有关该模式的更多介绍可以阅读 [深入理解JavaScript系列（32）：设计模式之观察者模式](http://www.cnblogs.com/TomXu/archive/2012/03/02/2355128.html)


##### Promise 对象

Promise 对象是 CommonJS 工作组提出的一种规范，目的是为异步操作提供统一接口。在 ECMAScript 6 之前 JavaScript 语言原生是不支持 Promise 对象的。

首先，它是一个对象，也就是说与其他 JavaScript 对象的用法，没有什么两样；其次，它起到代理作用，充当异步操作与回调函数之间的中介。它使得异步操作具备同步操作的接口，使得程序具备正常的同步运行的流程，回调函数不必再一层层嵌套。

有关更多异步处理可以参阅读下面给出的文章
[阮一峰 - Promise](http://javascript.ruanyifeng.com/advanced/promise.html)
[基于事件的 JavaScript 编程：异步与同步](http://www.oschina.net/translate/event-based-programming-what-async-has-over-sync)
[jQuery.Deferred对象](http://javascript.ruanyifeng.com/jquery/deferred.html)


#### JavaScript和有限状态机

有限状态机（Finite-state machine）是一个非常有用的模型，简单说，它有三个特征：

- 状态总数（state）是有限的
- 任一时刻，只处在一种状态之中
- 某种条件下，会从一种状态转变（transition）到另一种状态

有关该模式的实现，可以查看 [javascript-state-machine](https://github.com/jakesgordon/javascript-state-machine) ，在某些业务场景下应用，能更好的组织代码。

相关阅读：
[阮一峰 - JavaScript与有限状态机](http://javascript.ruanyifeng.com/advanced/fsm.html)
[深入理解JavaScript系列（43）：设计模式之状态模式](http://www.cnblogs.com/TomXu/archive/2012/04/18/2437099.html)


#### Web Worker

<http://javascript.ruanyifeng.com/htmlapi/webworker.html>
<https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Using_web_workers>
<http://www.alloyteam.com/2015/11/deep-in-web-worker/>
<http://www.ibm.com/developerworks/cn/web/1112_sunch_webworker/index.html>

