---
title: javaScript 设计模式系列之三：代理模式
date: 2017-09-01
---

### 介绍

[代理模式](https://zh.wikipedia.org/wiki/%E4%BB%A3%E7%90%86%E6%A8%A1%E5%BC%8F)为其他对象提供一种代理以控制对这个对象的访问。

![](/myimages/2017-09-01_01.png "代理模式的统一建模语言(UML)图")

根据代理模式的使用目的不同，代理模式又可以分为多种类型:
- 远程代理 (Remote Proxy)
- 虚拟代理 (Virtual Proxy) 如需要创建一个资源消耗较大的对象，先创建一个消耗相对较小的对象来表示，真实对象只在需要时才会被真正创建。
- 保护代理 (Protect Proxy) 用来控制真实对象访问的权限。
- 缓冲代理 (Cache Proxy) 为某一个目标操作的结果提供临时的存储空间，以便多个客户端可以共享这些结果。
- 智能引用代理（Smart Reference Proxy）当一个对象被引用时，提供一些额外的操作，例如将对象被调用的次数记录下来等。

### 虚拟代理示例

```js
/**
 * DynamicProxy abstract class.
 */
function DynamicProxy() {
  this.args = arguments;
  this.initialized = false;

  if(typeof this.class != 'function') {
    throw new Error('DynamicProxy: the class attribute must be set before calling the super-class constructor.');
  }

  // Create the methods needed to implement the same interface.
  for(var key in this.class.prototype) {
    // Ensure that the property is a function
    if(typeof this.class.prototype[key] !== 'function') {
      continue;
    }

    // Add the method.
    var that = this;
    (function(methodName) {
      that[methodName] = function() {
        if(!that.initialized) {
          return;
        }
        return that.subject[methodName].apply(that.subject, arguments);
      };
    })(key);
  }
};

DynamicProxy.prototype = {
  constructor: DynamicProxy,
  _initialize: function() {
    this.subject = {}; // Instantiate the class.
    this.class.apply(this.subject, this.args);
    this.subject.__proto__ = this.class.prototype;

    this.initialized = true;
  }
};

DynamicProxy.extend = function(subclass, superclass) {
  for(var key in superclass.prototype) {
    if(key !== 'constructor')
      subclass.prototype[key] = superclass.prototype[key];
  }
  subclass.superclass = superclass;
};
```

```js
/**
 * TestProxy class.
 */
function TestProxy() {
  this.class = TestClass;
  TestProxy.superclass.apply(this, arguments);
};
DynamicProxy.extend(TestProxy, DynamicProxy);

/**
 * Test class.
 * 
 * @param {string} name 
 */
function TestClass(name) {
  this.name = name || '';
}

TestClass.prototype.getName = function() {
  return this.name;
};
TestClass.prototype.setName = function(name) {
  this.name = name;
}
```

```js
var test = new TestProxy('Tom');
console.log(test.getName()); // undefined
test._initialize();
console.log(test.getName()); // Tom
test.setName('Mary');
console.log(test.getName()); // Mary
```

### 相关阅读
[史上最全设计模式导学目录（完整版）](http://blog.csdn.net/lovelion/article/details/17517213)