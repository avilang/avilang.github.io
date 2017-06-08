---
title: javaScript 设计模式系列之二：适配器模式
date: 2017-06-08
---

### 介绍

[适配器模式](https://zh.wikipedia.org/wiki/%E9%80%82%E9%85%8D%E5%99%A8%E6%A8%A1%E5%BC%8F)将一个类的接口转接成用户所期待的，有助于避免大规模改写现有客户代码。
> In software engineering, the adapter pattern is a software design pattern that allows the interface of an existing class to be used from another interface. It is often used to make existing classes work with others without modifying their source code. 

例：电源转换器，我们国家的电器使用普通的扁平两项或三项插头，而去外国的话，使用的标准就不一样了，比如德国，使用的是德国标准，是两项圆头的插头。如果去德国旅游，那么我们使用的手机充电器插头无法插到德国的插排中去，那就意味着我们无法给手机充电。怎样解决这个问题呢？只要使用一个电源转化器就行了。

### 例子：购物车

#### Shopping Cart Example with Local Storage

```js
function ShoppingCart() {}

ShoppingCart.prototype.add = function(item) {
  var items = localStorage.getItem('cart');
  if (items) {
    items = JSON.parse(items);

    if (items[item.id]) {
      items[item.id].quantity += 1;
    } else {
      item.quantity = 1;
      items[item.id] = item;
    }
  } else {
    items = {};
    item.quantity = 1;
    items[item.id] = item;
  }

  items = JSON.stringify(items);
  localStorage.setItem('cart', items);
  return item;
};
```

```js
var cart = new ShoppingCart();
cart.add({ id: 1, product: 'movie 1' }); // quantity is 1 for product 1
cart.add({ id: 2, product: 'movie 2' }); // quantity is 1 for product 2
cart.add({ id: 1, product: 'movie 1' }); // quantity is 2 for product 1
```

上面代码中通过 localStorage 存储购物车的数据。若此时改变需求，需要通过服务器端存储，这时你不得不改变现有的代码，此时更好的实现方式时引入"适配器"。

#### Shopping Cart Example with Adapters

```js
//  jQuery deferreds and promises
var localStorageAdapter = {
  findAll: function() {
    var deferred = new $.Deferred();
    var items = localStorage.getItem('cart');

    if (items) {
      items = JSON.parse(items);
    }

    deferred.resolve(items);
    return deferred.promise();
  },

  save: function(items) {
    var deferred = new $.Deferred();

    items = JSON.stringify(items);
    localStorage.setItem('cart', items);
    deferred.resolve();
    return deferred.promise();
  }
};
```

```js
//  jQuery deferreds and promises.
var serverSideAdapter = {
  findAll: function() {
    return $.ajax({
      url: '/shopping-cart'
    }).then(function(response) {
      return response.items;
    });
  },

  save: function(items) {
    return $.ajax({
      url: '/shopping-cart',
      type: 'post',
      data: {
        items: items
      }
    });
  }
};
```

```js
function ShoppingCart(adapter) {
  this.adapter = adapter;
}

ShoppingCart.prototype.add = function(item) {
  var adapter = this.adapter;
  var deferred = new $.Deferred();

  adapter.findAll().then(function(items) {
    if (items) {
      if (items[item.id]) {
        items[item.id].quantity += 1;
      } else {
        item.quantity = 1;
        items[item.id] = item;
      }
    } else {
      items = {};
      item.quantity = 1;
      items[item.id] = item;
    }

    adapter.save(items).then(function() {
      deferred.resolve(item);
    });
  });

  return deferred.promise();
};
```

```js
// localStorageAdapter
var cart = new ShoppingCart(localStorageAdapter);
cart.add({ id: 1, product: 'movie 1' }).then(function(item) { }); // quantity is 1 for product 1
cart.add({ id: 2, product: 'movie 2' }).then(function(item) { }); // quantity is 1 for product 2
cart.add({ id: 1, product: 'movie 1' }).then(function(item) { }); // quantity is 2 for product 1

// serverSideAdapter
var cart = new ShoppingCart(serverSideAdapter);
```

### 例子：日志记录

```js
// 当前 logger 
function BadLogger(name) {
    this.name = name;
    var LOG_HEADER = '[' + name + ']:';
    var self = this;

    return {
        getName: function getName() {
            return self.name;
        },
        getType: function getType() {
            return 'BadLogger';
        },
        information: function information(message) {
            console.info(LOG_HEADER + message + '- INFORMATION' );
        },
        debg: function debg(message) {
            console.log(LOG_HEADER + message + '- DEBG');
        },
        w: function w(message) { // w stands for warning.
            console.warn(LOG_HEADER + message + '- W' );
        },
        err: function err(message) {
            console.error(LOG_HEADER + message+ '- ERR' );
        }
    }
}

module.exports = {  
    getLogger: BadLogger
}
```

```js
// 另一个 logger
function ShortLogger(name) {  
    this.name = name;
    var LOG_HEADER = '[' + name + ']';
    var self = this;
    var getTime = function() {
        return '[' + new Date().toISOString() + ']';
    }
    return {
        getName: function getName() {
            return self.name;
        },
        getType: function getType() {
            return 'ShortLogger';
        },
        i: function i(message) {
            console.info(LOG_HEADER + getTime() + '[I]: ' + message);
        },
        d: function d(message) {
            console.log(LOG_HEADER + getTime() + '[D]: ' + message);
        },
        w: function w(message) {
            console.warn(LOG_HEADER + getTime() + '[W]: ' + message);
        },
        e: function e(message) {
            console.error(LOG_HEADER + getTime() + '[E]: ' + message);
        }
    }
}

module.exports = {  
    getLogger: ShortLogger
}
```

```js
var ShortLogger = require('./ShortLogger');  
var BadLogger = require('./BadLogger');

function LoggerAdapter(loggerObj) {  
    if (!loggerObj) {
        throw Error('Parameter [loggerObj] is not defined.');
    }
    console.log('[LoggerAdapter] is using Logger with name: ' + loggerObj.getName());
    var CONSTANTS = {
        DEBUG: 'DEBUG',
        WARNING: 'WARNING',
        INFORMATION: 'INFORMATION',
        ERROR: 'ERROR',
        BAD_LOGGER: 'BadLogger',
        SHORT_LOGGER: 'ShortLogger'
    };
    var loggerFunctionMapper = {};

    if(loggerObj.getType() === CONSTANTS.BAD_LOGGER) {
        loggerFunctionMapper[CONSTANTS.DEBUG] = loggerObj.debg;
        loggerFunctionMapper[CONSTANTS.INFORMATION] = loggerObj.information;
        loggerFunctionMapper[CONSTANTS.WARNING] = loggerObj.w;
        loggerFunctionMapper[CONSTANTS.ERROR] = loggerObj.err;
    }
    else if (loggerObj.getType() === CONSTANTS.SHORT_LOGGER) {
        loggerFunctionMapper[CONSTANTS.DEBUG] = loggerObj.d;
        loggerFunctionMapper[CONSTANTS.INFORMATION] = loggerObj.i;
        loggerFunctionMapper[CONSTANTS.WARNING] = loggerObj.w;
        loggerFunctionMapper[CONSTANTS.ERROR] = loggerObj.e;
    }

    function information(message) {
        try {
          loggerFunctionMapper[CONSTANTS.INFORMATION](message);
        }
        catch(err) {
            throw Error('No implementation for Logger: ' + loggerObj.toString());
        }
    };

    function debug(message) {
        try {
          loggerFunctionMapper[CONSTANTS.DEBUG](message);
        }
        catch(err) {
            throw Error('No implementation for Logger: ' + loggerObj.toString());
        }
    };
...
    return {
        debug: debug,
        information: information,
        warning: warning,
        error: error
    }
}

module.exports = {  
    LoggerAdapter: LoggerAdapter
}
```

```js
var ShortLogger = require('./ShortLogger');  
var BadLogger = require('./BadLogger');  
var LoggerAdapter = require('./LoggerAdapter');  
var shortLog = ShortLogger.getLogger('ShortLoger');  
var badLogger = BadLogger.getLogger('BadLogger');

var loggerAdapter = LoggerAdapter.LoggerAdapter(badLogger);  
loggerAdapter.information('This is logged through LoggerAdapter');  
loggerAdapter.debug('This is logged through LoggerAdapter');  
loggerAdapter.warning('This is logged through LoggerAdapter');  
loggerAdapter.error('This is logged through LoggerAdapter');

console.log();

var loggerAdapter2 = LoggerAdapter.LoggerAdapter(shortLog);  
loggerAdapter2.information('Now This is logged through LoggerAdapter');  
loggerAdapter2.debug('Now This is logged through LoggerAdapter');  
loggerAdapter2.warning('Now This is logged through LoggerAdapter');  
loggerAdapter2.error('Now This is logged through LoggerAdapter');
```

### 相关阅读
[The Adapter Pattern in JavaScript](http://thejsguy.com/2015/10/16/the-adapter-pattern-in-javascript.html)
[Design Patterns - The Adapter Pattern in JavaScript](http://dealwithjs.io/design-patterns-the-adapter-pattern-in-javascript/)