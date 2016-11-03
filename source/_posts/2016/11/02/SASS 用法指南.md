---
title: SASS 用法指南
date: 2016-11-02
categories: sass
---

### 什么是SASS

SASS 是一种 CSS 开发工具，提供了许多便利的写法，使得 CSS 编写变得简单和可维护。本文总结了 SASS 的常用用法，高手可略过，也可参考[官方文档](http://sass-lang.com/documentation/file.SASS_REFERENCE.html)

### 安装

使用 SASS 需要安装 Ruby ，Windows 用户可在这 [下载](http://rubyinstaller.org/downloads/) Ruby 的安装包。假定你已经安装好了 Ruby ，接着在命令行输入下面的命令进行安装。

```
$ gem install sass
```

可以通过 `sass -v` 来检测 SASS 是否安装成功。

由于国内网络原因，导致 rubygems.org 存放在 Amazon S3 上面的资源文件间歇性连接失败。这时我们可以利用淘宝提供的 [RubyGems 镜像](https://ruby.taobao.org/) ，在命令行输入下面命令。

```
$ gem sources --add https://ruby.taobao.org/ --remove https://rubygems.org/
$ gem sources -l
*** CURRENT SOURCES ***

https://ruby.taobao.org
```

若切换淘宝提供的镜像是提示 `SSL` `certificate verify failed` 相关的信息，那需要进一步的设置。下面是简单描述:

1. 到 `https://curl.haxx.se/ca/cacert.pem` 下载证书，一般放到 Ruby 安装的根目录即可。
2. 设置环境变量，变量名为 `SSL_CERT_FILE` 值为证书地址。如：`D:\Program Files\Ruby23-x64\cacert.pem`
3. 重启客户端，再重新设置
4. 可参考 <https://gist.github.com/fnichol/867550> 和 <http://www.csdn123.com/html/topnews201408/35/3635.htm>

另外提醒一下 `npm` 也存在同样的情况，也可换成 [淘宝 NPM 镜像](http://npm.taobao.org/)

```
$ npm config set registry https://registry.npm.taobao.org
$ npm config get registry
```

### 使用

SASS 文件是普通的文本文件，文件后缀名是 `.scss` ，我们需要将其编译成 `*.css` 的文件来使用，命令如下。

```
$ sass test.scss test.css
$ sass --style compressed test.sass test.css
```

SASS 提供四个[编译风格](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#output_style)的选项

```
1. nested：嵌套缩进的css代码，它是默认值。

2. expanded：没有缩进的、扩展的css代码。

3. compact：简洁格式的css代码。

4. compressed：压缩后的css代码。
```

你也可以让 SASS 监听某个文件或目录，一旦源文件有变动，就自动生成编译后的版本。

```
$ sass --watch input.scss:output.css  // watch a file

$ sass --watch app/sass:public/stylesheets  // watch a directory
```

按 `Ctrl + C` 可停止监听，另外还有一个[在线转换器](http://www.sassmeister.com/)，你可以在那里，试运行下面的例子。

### 基本用法

#### 1.注释

SASS 共有两种注释风格
* 标准的 CSS 注释 `/* comment */` ，会保留到编译后的文件。
* 单行注释 `// comment` ，只保留在 SASS 源文件中，编译后被省略。

在 `/*` 后面加一个感叹号，表示这是"重要注释"。即使是压缩模式编译，也会保留这行注释，通常可以用于声明版权信息。

```
/*!
  重要注释！
*/
```

#### 2.变量

SASS 允许使用变量，所有变量以 `$` 开头。如果变量需要镶嵌在字符串之中，就必须需要写在 `#{}` 之中。

```
$blue : #1875e7;
$side : left;

div {
  color : $blue;
}

.rounded {
  border-#{$side}-radius: 5px;
}
```

#### 3.计算功能

```
body {
　margin: (14px/2);
　top: 50px + 100px;
　right: 2 * 10%;
}
```

#### 4.嵌套

```
div {
    h1 {
        color:red;
    }
}
```

属性也可以嵌套

```
p {
    border:{
        width:1px;
        color:#f90;
        style:solid;
    }
}
```

在嵌套的代码块内，可以使用 `&` 引用父元素

```
a {
  color:#000;
  &:hover {
    color: green;
  }
}
```

#### 5.继承

```
.class1{
    width: 100px;
    height:100px;
}

.class2{
    @extend .class1;
    background-color: #fa0;
}
```

上面示例编译后

```
.class1, .class2 {
  width: 100px;
  height: 100px;
}

.class2 {
  background-color: #fa0;
}
```

#### 6.Mixin

```
@mixin rounded($vert, $horz, $radius:10px) {
　　　　border-#{$vert}-#{$horz}-radius: $radius;
　　　　-moz-border-radius-#{$vert}#{$horz}: $radius;
　　　　-webkit-border-#{$vert}-#{$horz}-radius: $radius;
}

div {
  @include rounded(top, left, 5px);
}
```

#### 7.插入文件

`@import` 命令，用来插入外部文件。如果插入的是 `.css` 文件，则等同于 css 的 import 命令。

```
@import '*.scss';
```

#### 8.自定义函数

```
@function double($n){
  @return $n * 2;
}

.testDouble{
  width:double(100px);
}
```

### 结语

更多特性可自行查看 [SASS 官网](http://sass-lang.com/)，和 SASS 类似的有 LESS ，可百度了解。另外还有一个强悍的 SASS 工具库 Compass

### 参考资料
<http://www.ruanyifeng.com/blog/2012/06/sass.html>
<http://www.w3cplus.com/sassguide/>
<http://www.sasschina.com/>
<http://sass.bootcss.com/>
