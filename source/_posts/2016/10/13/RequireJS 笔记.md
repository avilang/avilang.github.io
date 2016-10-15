---
title: RequireJS 笔记
date: 2016-10-13
categories: javascript
---

### 概述

RequireJS 是一个工具库，能在网页中进行模块化编程，从而提高代码的性能和可维护性。它的模块管理遵守 [AMD 规范](https://github.com/amdjs/amdjs-api/wiki/AMD-%28%E4%B8%AD%E6%96%87%E7%89%88%29)。类似的库还有 sea.js 国人开发的一个库，遵守 CMD 规范。

个人推荐使用 RequireJS 库，从功能上说，两者是差不多的，但 RequireJS 有完善的文档和很好的打包工具可用，相比 sea.js 强多了，在这里吐槽下，感觉之前被 sea.js 坑了不少。


### 使用

#### 加载 javascript 文件

RequireJS 以一个相对于 `baseUrl` 的地址来加载所有的代码。 页面顶层 `<script>` 标签含有一个特殊的属性 `data-main` 使用它来启动脚本加载过程，而 `baseUrl` 一般设置到与该属性相一致的目录。

```js
<script data-main="scripts/main.js" src="scripts/require.js"></script>
```

`baseUrl` 亦可通过 RequireJS config 手动设置。如果没有显式指定 config 及 data-main ，则默认的 baseUrl 为包含 RequireJS 的那个 HTML 页面的所属目录。

若符合下述规则之一，则可避开 `baseUrl + paths` 配置

- 以 ".js" 结束
- 以 "/" 开始
- 包含 URL 协议, 如 "http:" or "https:"

一般来说，最好还是使用 `baseUrl` 及 `paths` 去设置。它会给你带来额外的灵活性，如便于脚本的重命名、重定位等。同时，为了避免凌乱的配置，最好不要使用多级嵌套的目录层次来组织代码。


#### 模块的定义

1.Simple Name/Value Pairs

```js
define({
    color: "black",
    size: "unisize"
});
```

2.Definition Functions

```js
define(function () {
    // ...

    return {
        color: "black",
        size: "unisize"
    }
});
```

3.Definition Functions with Dependencies

```js
define(["./cart", "./inventory"], function(cart, inventory) {
        //return an object
        return {
            color: "blue",
            size: "large",
            addToCart: function() {
                inventory.decrement(this);
                cart.add(this);
            }
        }
    }
);
```

依赖关系会以参数的形式注入到函数上，参数列表与依赖名称列表一一对应。

4.Define a Module as a Function

```js
define(["my/cart", "my/inventory"],
    function(cart, inventory) {
        //return a function.
        //It gets or sets the window title.
        return function(title) {
            return title ? (window.title = title) :
                   inventory.storeName + ' ' + cart.name;
        }
    }
);
```

5.Define a Module with Simplified CommonJS Wrapper

```js
define(function(require, exports, module) {
        var a = require('a'),
            b = require('b');

        //Return the module value
        return function () {};
    }
);
```

利用 `Function.prototype.toString()` 实现，但在一些设备如 PS3 及一些老的 Opera 手机浏览器中不起作用。

6.Define a Module with a Name

```js
define("foo/title",
    ["my/cart", "my/inventory"],
    function(cart, inventory) {
        //...
   }
);
```

可看到一些 define() 中包含了一个模块名称作为首个参数，这些常由打包工具生成。你也可以自己显式指定模块名称，但这使模块更不具备移植性——就是说若你将文件移动到其他目录下，你就得重命名。


### Loader Plugins

有关 Loader Plugins 可跳转阅读 [http://requirejs.org/docs/api.html#plugins](http://requirejs.org/docs/api.html#plugins) 有关 requirejs 插件的编写可跳阅 [http://requirejs.org/docs/plugins.html#api](http://requirejs.org/docs/plugins.html#api)


### CommonJS Compatibility

```js
//BAD
var mod = require(someCondition ? 'a' : 'b');

//BAD
if (someCondition) {
    var a = require('a');
} else {
    var b = require('b');
```

以上的写法在 RequireJS 中是错误的用法，这样做的话, a 和 b 这两模块都会加载到页面中，这里可查看下文给出的列子。详细可点击阅读 [CommonJS Compatibility](http://requirejs.org/docs/whyamd.html#commonjscompat)
这种业务场景的解决方案 [callback-require](https://github.com/amdjs/amdjs-api/wiki/require)


### 机制

RequireJS 使用 head.appendChild() 将每一个依赖加载为一个 script 标签。
RequireJS 等待所有的依赖加载完毕，加载是异步的，计算出模块定义函数正确调用顺序，然后依次调用它们。


### 打包

RequireJS 提供一个基于 node.js 的命令行工具 r.js ，用来压缩多个 js 文件。它的主要作用是将多个模块文件压缩合并成一个脚本文件，以减少网页的HTTP请求数。例：

```js
node r.js -o baseUrl=. name=main out=main-built.js
```

除了直接在命令行提供参数设置，也可以将参数写入一个文件，假定文件名为 build.js

```js
node r.js -o build.js
```

有关更多的配置项可查看 [example.build.js](https://github.com/requirejs/r.js/blob/master/build/example.build.js) 和一个打包示例 [requirejs/example-multipage](https://github.com/requirejs/example-multipage)

进一步优化，使用 r.js 优化后的代码可以使用 [almond](https://github.com/requirejs/almond) 来加载。


### RequireJS 用法示例

例子中有对上文内容的一些补充，建议详细阅读代码。大家可点击 [例子](https://github.com/avilang/experience/tree/master/learn-requirejs) 打开看看，留意控制台的输出，注意这里为了演示就没有对 js 进行打包。


### 参考资料

<http://requirejs.org/>
<http://www.requirejs.cn/>
<http://javascript.ruanyifeng.com/tool/requirejs.html>
<http://leeluolee.github.io/2014/10/19/write-requirejs-plugin/>
<http://www.cnblogs.com/lhb25/p/requirejs-ptimizer-using.html>
