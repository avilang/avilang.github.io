---
title: javascript 正则表达式
date: 2016-10-08
---

### 前言

之前已写[《PHP 正则表达式笔记》](/blog/2016/PHP%20%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F%E7%AC%94%E8%AE%B0/)一文介绍正则表达式，本篇仅是作知识点的补充，文章逻辑编排版较随意。

### 创建一个正则表达式

你可以通过下面两种方法创建一个正则表达式:

1、使用一个正则表达式字面量，如下所示

```js
var re = /ab+c/;
```

2、调用 RegExp 对象的构造函数，如下所示

```js
var re = new RegExp("ab+c");
```

正则表达式字面量在脚本加载后编译,使用构造函数，提供了对正则表达式运行时的编译。同一个正则表达式可能会被编译多次，所以可将其保存在一个变量中以供后使用。
当你知道正则表达式的模式会发生改变，或者你事先并不了解它的模式或者是从其他地方（比如用户的输入），得到的代码这时比较适合用构造函数的方式。

有时候需要对正则字符串进行转义处理，下面给出一示例:

```js
function escapeRegExp(string){
  //$&表示被匹配的字符串
  return string.replace(/([.*+?^=!:${}()|[\]\/\\])/g, "\\$&");
}
```

摘自犀牛书：在 ECMAScript 3 规范中，用正则表达式创建的 RegExp 对象会共享一个实例，而在 ECMAScript 5中则是两个独立实例。

### 正则对象的属性和方法

javascript 语言里某种程度上可以认为 "一切皆对象"，那么 RegExp 对象也有其属性和方法。如 `ignoreCase` `global` `lastIndex` `match()` `exec()` 等属性和方法。

另外字符串的正则方法，如 `replace` `search` `split` `match` 方法，ES6 将这4个方法，语言内部全部调用 RegExp 的实例方法，从而做到所有与正则相关的方法，全都定义在 RegExp 对象上。

在这不具体介绍 javascript 正则对象的属性和方法的详细用法，读者可自行查阅。

### u 修饰符

ES6 对正则表达式添加了 `u` 修饰符，含义为“Unicode模式”，用来正确处理大于 `\uFFFF` 的 Unicode 字符。也就是说，会正确处理四个字节的 UTF-16 编码。

可点击查阅 [javascript 字符集](http://javascript.ruanyifeng.com/grammar/string.html#toc5) 了解更多 javascript 中字符的知识，学学如何处理四个字节的 UTF-16 编码。

### y 修饰符

ES6 还为正则表达式添加了 `y` 修饰符，叫做“粘连”（sticky）修饰符。

`y` 修饰符的一个应用，是从字符串提取 token（词元），`y` 修饰符确保了匹配之间不会有漏掉的字符。

```js
const TOKEN_Y = /\s*(\+|[0-9]+)\s*/y;
const TOKEN_G  = /\s*(\+|[0-9]+)\s*/g;

tokenize(TOKEN_Y, '3 + 4')
// [ '3', '+', '4' ]
tokenize(TOKEN_G, '3 + 4')
// [ '3', '+', '4' ]

function tokenize(TOKEN_REGEX, str) {
  let result = [];
  let match;
  while (match = TOKEN_REGEX.exec(str)) {
    result.push(match[1]);
  }
  return result;
}
```

上面代码中，如果字符串里面没有非法字符，y修饰符与g修饰符的提取结果是一样的。但是，一旦出现非法字符，两者的行为就不一样了。

```js
tokenize(TOKEN_Y, '3x + 4')
// [ '3' ]
tokenize(TOKEN_G, '3x + 4')
// [ '3', '+', '4' ]
```

### 结语

以下给出简单列子，说明了一些正值表达式的用途。本文中许多有关 javascript 正则中的知识点并未介绍到，如断言、组匹配、反向引用等。

```js
var names = "Harry Trump ;Fred Barney; Helen Rigby ; Bill Abel ; Chris Hand ";

var output = ["---------- Original String\n", names + "\n"];

var pattern = /\s*;\s*/;

var nameList = names.split(pattern);


pattern = /(\w+)\s+(\w+)/;


var bySurnameList = [];


output.push("---------- After Split by Regular Expression");

var i, len;
for (i = 0, len = nameList.length; i < len; i++){
  output.push(nameList[i]);
  bySurnameList[i] = nameList[i].replace(pattern, "$2, $1");
}


output.push("---------- Names Reversed");
for (i = 0, len = bySurnameList.length; i < len; i++){
  output.push(bySurnameList[i]);
}


bySurnameList.sort();
output.push("---------- Sorted");
for (i = 0, len = bySurnameList.length; i < len; i++){
  output.push(bySurnameList[i]);
}

output.push("---------- End");

console.log(output.join("\n"));
```

```js
// 问：请找出所有在 3 个连续相同字符前的相邻 3 个连续相同字符
var s = 'aaalllsss0tAAAnnn999';
var re = /(\w)\1{2}(?=(\w)\2{2})/g;
console.log(s.match(re));  // [ 'aaa', 'lll', 'AAA', 'nnn' ]
```

### 相关阅读

- [javascript - 中的正则表达式](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Regular_Expressions)
- [阮一峰 - 正则的扩展](http://es6.ruanyifeng.com/#docs/regex)
- [阮一峰 - 字符串](http://javascript.ruanyifeng.com/grammar/string.html)
- [正则表达式之：零宽断言](http://fxck.it/post/50558232873)
- [JS正则表达式一条龙讲解](https://segmentfault.com/a/1190000008088937)
- [正则表达式前端使用手册](http://louiszhai.github.io/2016/06/13/regexp/)