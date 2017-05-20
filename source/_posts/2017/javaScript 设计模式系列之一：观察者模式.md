---
title: javaScript 设计模式系列之一：观察者模式
date: 2017-05-20
---

### 介绍

[观察者模式](https://zh.wikipedia.org/wiki/%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F)又叫发布订阅模式(Publish/Subscribe)，一个目标对象管理所有相依于它的观察者对象。该模式中存在两个角色：观察者和被观察者。目标对象与观察者之间的抽象耦合关系能够单独扩展以及重用。

该模式通常有两种实现策略："推"(push) 和 "拉"(pull)
> 例：对于报社可能雇佣投送人员四处送报给订阅者，这就是主动推送。而对于规模小的报社没有足够的资源去雇佣投送员，这时候可以采取 "拉" 的方式。就是在订阅者附近提供自己的数据，供订阅者 "拉" 自己所需的数据。

### 实现

#### push

```js
var Observable = {
    observers: []
  , lastId: -1
  , addObserver: function(observer) {
      this.observers.push({
        callback: observer
      , id: ++this.lastId
      })

      return this.lastId
    }
  , removeObserver: function(id) {
      for (var i = this.observers.length - 1; i >= 0; i--) {
        this.observers[i]
        if (this.observers[i].id == id) {
          this.observers.splice(i, 1)
          return true
        }
      }

      return false
    }
  , notifyObservers: function(message) {
      for (var i = this.observers.length - 1; i >= 0; i--) {
        this.observers[i].callback(message)
      };
    }
}

var id_1 = Observable.addObserver(function(message){
  console.log("First observer message:" + message)
})

var observer = function(message){
  console.log("Second observer message:" + message)
}

var id_2 = Observable.addObserver(observer)

Observable.notifyObservers('test 1')
Observable.removeObserver(id_2)
Observable.notifyObservers('test 2')
```

#### pull

```js
var Observable = {}

;(function(O){
  var observers = []
    , privateVar

  O.addObserver = function(observer) {
    observers.push(observer)
  }

  O.removeObserver = function(observer) {
    var index = observers.indexOf(observer)

    if (~index) {
      observers.splice(index, 1)
    }
  }

  O.notifyObservers = function() {
    for (var i = observers.length - 1; i >= 0; i--) {
      observers[i].update()
    };
  }

  O.updatePrivate = function(newValue) {
    privateVar = newValue
    this.notifyObservers()
  }

  O.getPrivate = function() {
    return privateVar
  }
}(Observable))

Observable.addObserver({
  update: function(){
    this.process()
  }
, process: function(){
    var value = Observable.getPrivate()
    console.log("Private value is: " + value)
  }
})

Observable.updatePrivate('test 1')
// Private value is: test 1

Observable.updatePrivate('test 2')
// Private value is: test 2
```

被观察对象将通知所有的观察者，并且每个观察者将从被观察对象中提取所需的信息。

#### 让多个对象都具有观察者发布订阅的功能

```js
var observer = {
    addSubscriber:function (callback) {
        this.subscribers[this.subscribers.length] = callback;
    },
    removeSubscriber:function (callback) {
        for (var i = 0; i < this.subscribers.length; i++) {
            if (this.subscribers[i] === callback) {
                delete(this.subscribers[i]);
            }
        }
    },
    publish:function (what) {
        for (var i = 0; i < this.subscribers.length; i++) {
            if (typeof this.subscribers[i] === 'function') {
                this.subscribers[i](what);
            }
        }
    },
    make:function (o) { // turns an object into a publisher
        for (var i in this) {
            o[i] = this[i];
            o.subscribers = [];
        }
    }
};

var blogger = {
    writeBlogPost:function () {
        var content = 'Today is ' + new Date();
        this.publish(content);
    }
};
var la_times = {
    newIssue:function () {
        var paper = 'Martians have landed on Earth!';
        this.publish(paper);
    }
};

observer.make(blogger);
observer.make(la_times);

var jack = {
    read:function (what) {
        console.log('I just read that ' + what)
    }
};
var jill = {
    gossip:function (what) {
        console.log('You didn\'t hear it from me, but ' + what)
    }
};

blogger.addSubscriber(jack.read);
blogger.addSubscriber(jill.gossip);
blogger.writeBlogPost();
blogger.removeSubscriber(jill.gossip);
blogger.writeBlogPost();
la_times.addSubscriber(jill.gossip);
la_times.newIssue();
```

#### 避免创建多个被观察者对象，可以增加 "命名空间"

```js
var Observable = {
    observers: []
  , addObserver: function(topic, observer) {
      this.observers[topic] || (this.observers[topic] = [])

      this.observers[topic].push(observer)
    }
  , removeObserver: function(topic, observer) {
      if (!this.observers[topic])
        return;

      var index = this.observers[topic].indexOf(observer)

      if (~index) {
        this.observers[topic].splice(index, 1)
      }
    }
  , notifyObservers: function(topic, message) {
      if (!this.observers[topic])
        return;

      for (var i = this.observers[topic].length - 1; i >= 0; i--) {
        this.observers[topic][i](message)
      };
    }
}

Observable.addObserver('cart', function(message){
  console.log("First observer message:" + message)
})

Observable.addObserver('notificatons', function(message){
  console.log("Second observer message:" + message)
})

Observable.notifyObservers('cart', 'test 1')
// First observer message:test 1

Observable.notifyObservers('notificatons', 'test 2')
// Second observer message:test 2
```

### 相关阅读
[javascript-observer-publish-subscribe-pattern](https://bumbu.github.io/javascript-observer-publish-subscribe-pattern/) 
[深入理解JavaScript系列（32）：设计模式之观察者模式](http://www.cnblogs.com/TomXu/archive/2012/03/02/2355128.html) 
[javascript-patterns-observer](https://github.com/shichuan/javascript-patterns/blob/master/design-patterns/observer.html)