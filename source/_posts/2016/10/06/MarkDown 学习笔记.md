---
title: MarkDown 学习笔记
date: 2016-10-06
---

MarkDown是一种适用于网络的书写语言，可以帮助你快速书写文档，不必再纠结文档排版的问题。并且它的语法简单，学习成本低，程序员必备技能...助你快速书写技术文档、文章。

用于书写 MarkDown 的编辑器有很多，这里就不在述说，不知道的码农可自行百度。在这我推荐使用 Sublime Text 3 这款编辑器，速度快，并且有大量的插件可用，能满足你日常编程的需求。个人使用 MarkdownEditing 和 MarkdownPreview 这两个插件来帮助编写 MarkDown 文件。

可以利用 Chrome 浏览器，点击打印另存为 PDF 格式文件，这样分享给其他人阅读。

### MarkDown语法说明

#### 一、标题

行首插入 1 到 6 个 `#`，对应到标题 `h1` 到 `h6`

```
# 这是 H1
## 这是 H2
###### 这是 H6
```

#### 二、段落和换行

一个 MarkDown 段落是由一个或多个连续的文本行组成，它的前后要有一个以上的空行（空行的定义是显示上看起来像是空的，便会被视为空行。比方说，若某一行只包含空格和制表符，则该行也会被视为空行）。普通段落不该用空格或制表符来缩进。

如果你确实想要依赖 MarkDown `<br>` 来插入标签的话，在插入处*先按入两个以上的空格然后回车*。

#### 三、区块引用 Blockquotes

MarkDown 文件中建立一个区块引用语法很简单，只需在每行的最前面加上 `>` 符号。区块引用还可以嵌套（例如：引用内的引用），只要根据层次加上不同数量的 `>`

```
> This is a blockquote with two paragraphs. Lorem ipsum dolor sit amet,
> consectetuer adipiscing elit. Aliquam hendrerit mi posuere lectus.

> This is the first level of quoting.
>
> > This is nested blockquote.
>
> Back to the first level
```

#### 四、列表
MarkDown 支持有序列表和无序列表，无序列表使用星号、加号或是减号作为列表标记。

```
*   Red
*   Green
*   Blue

+   Red
+   Green
+   Blue

-   Red
-   Green
-   Blue
```

有序列表则使用数字接着一个英文句点。
```
1.  Bird
2.  McHale
3.  Parish
```

#### 五、代码

和程序相关的写作或是标签语言原始码通常会有已经排版好的代码区块，通常这些区块我们并不希望它以一般段落文件的方式去排版，而是照原来的样子显示 MarkDown 会用 `<pre>` 和 `<code>` 标签来把代码区块包起来。

要在 Markdown 中建立代码区块很简单，只要简单地缩进 4 个空格或是 1 个制表符就可以，例如，下面的输入：

```
这是一个普通段落

    这是一个代码区块
```

MarkDown 会转换成：

```
<p>这是一个普通段落</p>

<pre>这是一个代码区块</pre>
```

如果要标记一小段行内代码，你可以用反引号把它包起来

```
Use the `printf()` function.
```

如果要在代码区段内插入反引号，你可以用多个反引号来开启和结束代码区段

```
``There is a literal backtick (`) here.``
```


#### 六、连接

*行内式* 要建立一个行内式的链接，只要在方块括号后面紧接着圆括号并插入网址链接即可，如果你还想要加上链接的 title 文字，只要在网址后面，用双引号把 title 文字包起来即可。

```
This is [an example](http://example.com/ "Title") inline link.  
See my [About](/about/) page for details.
```

*参考式* 的链接是在链接文字的括号后面再接上另一个方括号，而在第二个方括号里面要填入用以辨识链接的标记，你也可以选择性地在两个方括号中间加上一个空格。接着，在文件的任意处，你可以把这个标记的链接内容定义出来。例：

```
This is [an example] [id] reference-style link.
[id]: http://example.com/  "Optional Title Here"
```

链接内容定义的形式为：
1. 方括号（前面可以选择性地加上至多三个空格来缩进），里面输入链接文字
2. 接着一个冒号
3. 接着一个以上的空格或制表符
4. 接着链接的网址
5. 选择性地接着 title 内容，可以用单引号、双引号或是括弧包着，有一个已知的问题是 Markdown.pl 1.0.1 会忽略单引号包起来的链接 title

#### 七、图片

```
行内式

![Alt text](//www.baidu.com/img/bd_logo1.png "百度")

![](file:///D:/xampp/htdocs/test/tmp/lessoo2o/20160402092920.png)
```

1. 一个惊叹号`!`
2. 接着一个方括号，里面放上图片的替代文字
3. 接着一个普通括号，里面放上图片的网址，最后还可以用引号包住并加上 选择性的 'title' 文字。

```
参考式

![Alt text][id]
[id]: url/to/image  "Optional title attribute"
```

「id」是图片参考的名称，图片参考式的定义方式则和连接参考式一样。

#### 八、反斜杠

MarkDown 可以利用反斜杠来插入一些在语法中有其它意义的符号，例如：如果你想要用星号加在文字旁边的方式来做出强调效果（但不用`<em>`标签），你可以在星号的前面加上反斜杠：

```
\*literal asterisks\*
```

MarkDown 支持以下这些符号前面加上反斜杠来帮助插入普通的符号：

```
\   反斜线
`   反引号
*   星号
_   底线
{}  花括号
[]  方括号
()  括弧
#   井字号
+   加号
-   减号
.   英文句点
!   惊叹号
```


#### 九、自动链接

MarkDown 支持以比较简短的自动链接形式来处理网址和电子邮件信箱，只要是用方括号包起来， MarkDown 就会自动把它转成链接。一般网址的链接文字就和链接地址一样，例如：

```
<http://example.com/>
<address@example.com>
```

### 参考资料
<http://www.appinn.com/markdown/index.html>