---
title: 聊聊 S.O.L.I.D 五大原则
date: 2017-04-22
---

### 前言

今天聊聊在面向对象编程中的五个基本原则，适当的利用这些原则，让代码变得可维护和扩展。本文以 javascript 语言为例，可能以这门语言来讨论 S.O.L.I.D 五大原则有点不伦不类，但其中的思想是很必要去学习和理解的。

### 单一职责(Single Responsibility Principle)

[单一职责](https://zh.wikipedia.org/wiki/%E5%8D%95%E4%B8%80%E5%8A%9F%E8%83%BD%E5%8E%9F%E5%88%99)的描述如下：

> A class should have only one reason to change
> 类发生更改的原因应该只有一个

遵守单一职责的好处是可以让我们很容易地来维护这个对象，当一个对象封装了很多职责的话，一旦一个职责需要修改，势必会影响该对象想的其它职责代码。通过解耦可以让每个职责工更加有弹性地变化。

相关阅读：[深入理解JavaScript系列（6）：S.O.L.I.D五大原则之单一职责SRP](http://www.cnblogs.com/TomXu/archive/2012/01/06/2305513.html)

### 开闭原则(Open/Closed Principle)

[开闭原则](https://zh.wikipedia.org/wiki/%E5%BC%80%E9%97%AD%E5%8E%9F%E5%88%99)的描述如下：

> Software entities (classes, modules, functions, etc.) should be open for extension but closed for modification.
> 软件实体（类，模块，方法等等）应当对扩展开放，对修改关闭，即软件实体应当在不修改的前提下扩展。

为了直观地描述，下面给出一个动态展示问题列表的代码作为演示（不符合开闭原则）

```js
// 问题类型
var AnswerType = {
    Choice: 0,
    Input: 1
};

// 问题实体
function question(label, answerType, choices) {
    return {
        label: label,
        answerType: answerType,
        choices: choices // 是可选参数
    };
}

var view = (function () {
    // render
    function renderQuestion(target, question) {
        var questionWrapper = document.createElement('div');
        questionWrapper.className = 'question';

        var questionLabel = document.createElement('div');
        questionLabel.className = 'question-label';
        var label = document.createTextNode(question.label);
        questionLabel.appendChild(label);

        var answer = document.createElement('div');
        answer.className = 'question-input';

        // 根据不同的类型展示不同的代码：分别是下拉菜单和输入框两种
        if (question.answerType === AnswerType.Choice) {
            var input = document.createElement('select');
            var len = question.choices.length;
            for (var i = 0; i < len; i++) {
                var option = document.createElement('option');
                option.text = question.choices[i];
                option.value = question.choices[i];
                input.appendChild(option);
            }
        }
        else if (question.answerType === AnswerType.Input) {
            var input = document.createElement('input');
            input.type = 'text';
        }

        answer.appendChild(input);
        questionWrapper.appendChild(questionLabel);
        questionWrapper.appendChild(answer);
        target.appendChild(questionWrapper);
    }

    return {
        // 遍历所有的问题列表进行渲染
        render: function (target, questions) {
            for (var i = 0; i < questions.length; i++) {
                renderQuestion(target, questions[i]);
            };
        }
    };
})();

var questions = [
    question('Have you used tobacco products within the last 30 days?',AnswerType.Choice, ['Yes', 'No']),
    question('What medications are you currently using?', AnswerType.Input)
];

var questionRegion = document.getElementById('questions');
view.render(questionRegion, questions);
```

上面代码有一个限制，就是如果再增加一个问题类型的话，那就需要再次修改`renderQuestion`里的条件语句，这明显违反了开闭原则。重构后的代码如下：（符合开闭原则）

```js
function questionCreator(spec, my) {
    var that = {};

    my = my || {};
    my.label = spec.label;

    my.renderInput = function() {
        throw "not implemented";
    };

    that.render = function(target) {
        var questionWrapper = document.createElement('div');
        questionWrapper.className = 'question';

        var questionLabel = document.createElement('div');
        questionLabel.className = 'question-label';
        var label = document.createTextNode(spec.label);
        questionLabel.appendChild(label);

        var answer = my.renderInput();

        questionWrapper.appendChild(questionLabel);
        questionWrapper.appendChild(answer);
        return questionWrapper;
    };

    return that;
}

function choiceQuestionCreator(spec) {

    var my = {},
        that = questionCreator(spec, my);

    my.renderInput = function() {
        var input = document.createElement('select');
        var len = spec.choices.length;
        for (var i = 0; i < len; i++) {
            var option = document.createElement('option');
            option.text = spec.choices[i];
            option.value = spec.choices[i];
            input.appendChild(option);
        }

        return input;
    };

    return that;
}

function inputQuestionCreator(spec) {

    var my = {},
        that = questionCreator(spec, my);

    my.renderInput = function() {
        var input = document.createElement('input');
        input.type = 'text';
        return input;
    };

    return that;
}

var view = {
    render: function(target, questions) {
        for (var i = 0; i < questions.length; i++) {
            target.appendChild(questions[i].render());
        }
    }
};

var questions = [
    choiceQuestionCreator({
        label: 'Have you used tobacco products within the last 30 days?',
        choices: ['Yes', 'No']
    }),
    inputQuestionCreator({
        label: 'What medications are you currently using?'
    })
];

var questionRegion = document.getElementById('questions');
view.render(questionRegion, questions);
```

重构后的代码可以很容易的地进行扩展了，从而达到了开闭原则的要求。

相关阅读：[深入理解JavaScript系列（7）：S.O.L.I.D五大原则之开闭原则OCP](http://www.cnblogs.com/TomXu/archive/2012/01/09/2306329.html)

### 里氏替换原则(Liskov Substitution Principle)

[里氏替换原则](https://zh.wikipedia.org/wiki/%E9%87%8C%E6%B0%8F%E6%9B%BF%E6%8D%A2%E5%8E%9F%E5%88%99)的描述如下：

> Subtypes must be substitutable for their base types.
> 派生类型必须可以替换它的基类型

#### 正方形和矩形

```js
// 矩形对象
var rectangle = {
    length: 0,
    width: 0
};

// 正方形(宽高一致)
var square = {};
(function() {
    var length = 0, width = 0;
    Object.defineProperty(square, "length", {
        get: function() { return length; },
        set: function(value) { length = width = value; }
    });
    Object.defineProperty(square, "width", {
        get: function() { return width; },
        set: function(value) { length = width = value; }
    });
})();

var g = function(rectangle) {
    rectangle.length = 3;
    rectangle.width = 4;
    console.log(rectangle.length);
    console.log(rectangle.width);
    console.log(rectangle.length * rectangle.width);
};

// 正方形代替矩形
g(square);
```

当我们使用正方形代替矩形执行代码的时，会发现得到的结果并不是我们所期待的结果，这明显违法了里氏替换原则。

#### 鸵鸟到底是不是鸟？

一讲到鸟，就认为它能飞，有的鸟确实能飞，但不是所有的鸟都能飞。问题就是出在这里。如果以“飞”的行为作为衡量“鸟”的标准的话，鸵鸟显然不是鸟。如果按照生物学的划分标准：有翅膀、有羽毛等特性作为衡量“鸟”的标准的话，鸵鸟理所当然就是鸟了。鸵鸟没有“飞”的行为，我们强行给它加上了这个行为，所以在面对“飞越太平洋”的需求时，代码就会出现运行期故障，故设计要依赖于用户要求和具体环境而定。

#### 相关阅读

[深入理解JavaScript系列（8）：S.O.L.I.D五大原则之里氏替换原则LSP](http://www.cnblogs.com/TomXu/archive/2012/01/10/2310244.html)
[架构师之路之里氏代换原则](http://blog.csdn.net/Bitou_Von/article/details/4210654)

### 接口隔离原则(Interface Segregation Principle)

[接口隔离原则](https://zh.wikipedia.org/wiki/%E6%8E%A5%E5%8F%A3%E9%9A%94%E7%A6%BB%E5%8E%9F%E5%88%99)的描述如下：

> Clients should not be forced to depend on methods they do not use.
> 不应该强迫客户依赖于它们不用的方法

目前为止(2017-04-22)，JavaScript 没有接口的特性，不像 Java 等一些语言原生就提供接口的特性。当然 JavaScript 也有自己的一些实现，有兴趣的读者可参阅《JavaScript设计模式》一书（作者：Ross Harmes），其中有探讨在 JavaScript 中模仿接口的实现。

相关阅读：[深入理解JavaScript系列（21）：S.O.L.I.D五大原则之接口隔离原则ISP](http://www.cnblogs.com/TomXu/archive/2012/02/14/2330137.html)

### 依赖倒置原则(Dependency Inversion Principle)

[依赖倒置原则](https://zh.wikipedia.org/wiki/%E4%BE%9D%E8%B5%96%E5%8F%8D%E8%BD%AC%E5%8E%9F%E5%88%99)的描述如下：

> A. High-level modules should not depend on low-level modules.  Both should depend on abstractions.
> 高层模块不应该依赖于低层模块，二者都应该依赖于抽象
> 
> B. Abstractions should not depend upon details.  Details should depend upon abstractions.
> 抽象不应该依赖于细节，细节应该依赖于抽象

依赖倒置原则的最重要问题就是确保应用程序或框架的主要组件从非重要的底层组件实现细节解耦出来，这将确保程序的最重要的部分不会因为低层次组件的变化修改而受影响。下面给出一个示例：

```js
$.fn.trackMap = function(options) {
    var defaults = {
        /* defaults */
    };
    options = $.extend({}, defaults, options);

    var mapOptions = {
        center: new google.maps.LatLng(options.latitude,options.longitude),
        zoom: 12,
        mapTypeId: google.maps.MapTypeId.ROADMAP
    },
        map = new google.maps.Map(this[0], mapOptions),
        pos = new google.maps.LatLng(options.latitude,options.longitude);

    var marker = new google.maps.Marker({
        position: pos,
        title: options.title,
        icon: options.icon
    });

    marker.setMap(map);

    options.feed.update(function(latitude, longitude) {
        marker.setMap(null);
        var newLatLng = new google.maps.LatLng(latitude, longitude);
        marker.position = newLatLng;
        marker.setMap(map);
        map.setCenter(newLatLng);
    });

    return this;
};

var updater = (function() {
    // private properties
    return {
        update: function(callback) {
            updateMap = callback;
        }
    };
})();

$("#map_canvas").trackMap({
    latitude: 35.044640193770725,
    longitude: -89.98193264007568,
    icon: 'http://bit.ly/zjnGDe',
    title: 'Tracking Number: 12345',
    feed: updater
});
```

上面代码中 trackMap 函数有2个依赖：第三方的Google Maps API和Location feed。如果需要切换不同的地图提供商的话那就不得不对trackMap函数进行重写。对此我们应该实现一个适配Google Maps API的对象。如下：

```js
$.fn.trackMap = function(options) {
    var defaults = {
        /* defaults */
    };

    options = $.extend({}, defaults, options);

    options.provider.showMap(
        this[0],
        options.latitude,
        options.longitude,
        options.icon,
        options.title);

    options.feed.update(function(latitude, longitude) {
        options.provider.updateMap(latitude, longitude);
    });

    return this;
};

$("#map_canvas").trackMap({
    latitude: 35.044640193770725,
    longitude: -89.98193264007568,
    icon: 'http://bit.ly/zjnGDe',
    title: 'Tracking Number: 12345',
    feed: updater,
    provider: trackMap.googleMapsProvider
});
````

```js
trackMap.googleMapsProvider = (function() {
    var marker, map;

    return {
        showMap: function(element, latitude, longitude, icon, title) {
            var mapOptions = {
                center: new google.maps.LatLng(latitude, longitude),
                zoom: 12,
                mapTypeId: google.maps.MapTypeId.ROADMAP
            },
                pos = new google.maps.LatLng(latitude, longitude);

            map = new google.maps.Map(element, mapOptions);

            marker = new google.maps.Marker({
                position: pos,
                title: title,
                icon: icon
            });

            marker.setMap(map);
        },
        updateMap: function(latitude, longitude) {
            marker.setMap(null);
            var newLatLng = new google.maps.LatLng(latitude,longitude);
            marker.position = newLatLng;
            marker.setMap(map);
            map.setCenter(newLatLng);
        }
    };
})();
```

相关阅读：[深入理解JavaScript系列（22）：S.O.L.I.D五大原则之依赖倒置原则DIP](http://www.cnblogs.com/TomXu/archive/2012/02/15/2330143.html)