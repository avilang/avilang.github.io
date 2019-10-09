---
title: Sass 之 Compass 用法指南
date: 2016-11-03
---

### 什么是Compass

简单的说 Compass 是 Sass 的工具库，有点像 Javascript 和 JQuery 的关系。学会了 Compass 能大大提升 CSS 的开放效率。若不清楚什么是 Sass ，可移步阅读 [SASS 用法指南](/blog/2016/SASS%20%E7%94%A8%E6%B3%95%E6%8C%87%E5%8D%97/)

### 安装

Compass 是用 Ruby 语言开发的，所以安装它之前，必须安装 Ruby 。在命名行下输入

```
$ gem install compass
```

### 项目初始化

要创建一个你的 Compass 项目，假定它的名字叫做 testCompass，那么在命令行键入

```
$ compass create testCompass
```

当前目录就会生成 testCompass 的子目录。进入到该目录，你会看到一个 `config.rb` 文件，这是你项目的配置文件。还有两个子目录 `sass` 和 `stylesheets` 前者存放 Sass 源文件，后者存放编译后的 css 文件。

### 编译

要在网站上使用，必须将 scss 后缀的文件编译成 css 文件。编译命令如下

```
$ compass compile
```

该命令在项目根目录下运行，会将 sass 子目录中的 scss 文件，编译成 css 文件，保存在 stylesheets 子目录中。

默认状态下，编译出来的 css 文件带有大量的注释。但在生产环境需要压缩后的 css 文件，这时要使用 `--output-style` 参数

```
$ compass compile --output-style compressed
```

Compass 只编译发生变动的文件，如果你要重新编译未变动的文件，需要使用 `--force` 参数

```
$ compass compile --force
```

除了使用命令行参数，还可以在配置文件 `config.rb` 中指定编译模式。

```
output_style = :expanded
```

`:expanded` 模式表示编译后保留原格式，其他值还包括 `:nested` `:compact` 和 `:compressed`

也可以通过指定 environment 的值（ `:production` 或 `:development` ），智能判断编译模式。

```
environment = :development

output_style = (environment == :production) ? :compressed : :expanded
```

在命令行模式下，除了一次性编译命令，compass 还有自动编译命令

```
$ compass watch
```

运行该命令后，只要 scss 文件发生变化，就会被自动编译成 css 文件。按 `Ctrl + C` 退出监听。

有官更多 compass 的用法，请到[官网](http://compass-style.org/)查阅

注意：在 sass 目录下 若文件以 `_` 开头，如 _mixin.scss, _variables.scss, _icons.scss 则编译时不会生成 css 文件到 stylesheets 目录。这特性可结合 `@import` 使用。

### 使用

Compass 采用模块结构，不同模块提供不同的功能。如 reset, css3, layout, typography, utilities 这些模块。具体怎么用，可参考[官方文档](http://compass-style.org/reference/compass/)

下面给出一个示例代码，简单展示下 Compass 的用法

```
//引入 compass 也可指定引入某模块如  @import "compass/reset";
@import "compass";


// normalize 是第三方模块 使用前需要安装好，并在 config.rb 文件中 添加 require "compass-normalize"
//@import "normalize";


//浏览器的支持情况
//@debug browsers();
//@debug browser-versions(ie);
//$supported-browsers : chrome;
//$browser-minimum-versions:("ie":"8");
//@debug omitted-usage('ie', '6');  //0.0093 使用率

@include establish-baseline();

body{
  @include debug-vertical-alignment;
}

//自动添加浏览器的前缀
.radius{
    @include border-radius(4px);
}

.transparent{
    @include opacity(0.5);
}

.inline-block{
    @include inline-block;
}

a{
  @include link-colors(#000, #0cc, #0ca, #c0a, #fa0);
  @include unstyled-link();
}

//强行换行
.force-wrap{
  @include force-wrap();
}

.nowrap{
  @include nowrap;
}

//不换行 长度超出变成 ...
.ellipsis{
  @include ellipsis;
}

//若只兼容 ie8 以上 则编译后的 css 不出现 display: inline; （双边距）
.left{
  @include float('left');
}

.article{
  h1,h2,h3,p{
    margin:0;
  }
  h1{
    //font-size:3em;  //48 / 16 = 3
    //line-height: 1.5em; // 72 / 48 = 1.5
    @include adjust-font-size-to(48px);
  }
  h2{
    //font-size:2.25em;  //36 / 16 = 2.25
    //line-height: 1.333333em;  // 48 / 36 = 1.333333
    @include adjust-font-size-to(36px);
  }
  h3{
    //font-size:1.5em;  //24 / 16 = 1.5
    //line-height: 2em;  // 48 / 24 = 2
    @include adjust-font-size-to(24px);
  }
  p{
    // font-size:1em;
    // line-height: 1.5em;
    @include adjust-font-size-to(16px);
    @include leader();
    @include trailer();
  }
}

//base64
.img1{
    background-image: inline-image("2.jpeg");
}

.img2{
    background-image: image-url("0.jpg");
}

.tableStyle{

   //外边框
   @include outer-table-borders(2px, #f60);

   //单元格边框
   @include inner-table-borders(1px, #000);

   //格式化
   @include table-scaffolding;

   @include alternating-rows-and-columns(#fff, #f90, #222);
}

//清楚浮动
.clearfix{
  @include legacy-pie-clearfix();
}

.minH{
  @include min-height(30px);
}

//精灵图
@import 'icons';
```

其中 `_icons.scss` 的代码如下

```
//为 true 则样式中加上图片宽高
$icons-sprite-dimensions : true;

//合成布局 水平(horizontal) 垂直 对角线(diagon) 最节省空格 smart
$icons-layout : horizontal;

//间隔
$icons-spacing : 30px;

//更多参数参考 http://compass-style.org/help/tutorials/spriting/customization-options/

@import "icons/*.png";

@include all-icons-sprites();

//这样使类名 main-logo 有 s03 的样式
.main-logo{
    @include icons-sprite('s03');
}

//也可以生成带伪类的样式 图片命名 如 a.png 和 a_hover.png
```

### 结语
通过 sass 和 less 等这种 css 预处理器，可以让我们更高效多书写样式。另外要提一下的就是 [postcss](https://github.com/postcss/postcss) 相关介绍和教程可跳阅读 <http://www.w3cplus.com/blog/tags/516.html>

### 相关阅读

- [阮一峰 - Compass 用法指南](http://www.ruanyifeng.com/blog/2012/11/compass.html)