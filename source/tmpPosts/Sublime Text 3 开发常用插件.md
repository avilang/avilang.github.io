### 前言

今天聊聊 Sublime Text 3 这款编辑器，在此之前用过好几款编辑器如：Dreamweaver notepad++ 等。只能说 Sublime Text 3 太强悍了，很多插件可用，对于提高编码的速度和质量有很大的帮助。适合新老手的朋友用。据说 vim 也是神器。

### 开发常用插件

##### Package Control
Package Control 插件本身是一个为了方便管理插件的插件。至于安装方法，大家可以自行百度，这里就不多说了。

##### IMESupport
Sublime Text 3 下不支持中文的鼠标跟随，可以通过这个插件解决这问题。

##### SideBarEnhancements
侧栏右键功能增强插件

##### AutoFileName
这插件能自动完成文件名的输入，如图片选取等。 Sublime Text 3 默认没有这功能，使用该插件能大大方便编码。

##### DocBlockr
能生成标准的注释，包括函数名、参数、返回值等，并以多行显示，手动写比较麻烦。

##### Emmet
Emmet 作为 zen coding 的升级版，对于前端来说，可是必备插件。 可参考 [官网](http://docs.emmet.io/) 和 [官方语法速查表](http://docs.emmet.io/cheat-sheet/) 还有 [中文文档](http://yanxyz.github.io/emmet-docs/)

##### JavaScript Completions
一款 JS 语法提示插件

##### jQuery
jQuery 语法提示插件

##### JsFormat
JS 格式化插件，也可以把 JSON 数据格式化

##### Markdown Preview
可以把 markdown 文件转换成 html 格式

##### MarkdownEditing
Markdown 辅助编辑插件

##### Sass
sass 语法高亮插件

##### CSScomb
css 格式化 和 属性排序插件，排序的功能很赞，不紧可以排序 *.css 的文件，还能排序 *.scss 的文件。帮助你写出高效整洁的 css 代码。需要 Node.js 的支持。

##### Local History
非常推荐，智能缓存编辑过的文件，有点像本地版本管理工具。其中增加 `history_path` 配置项来设定缓存路径

##### EditorConfig
EditorConfig 可以帮助开发者在不同的编辑器和IDE之间定义和维护一致的代码风格，具体可跳阅 <http://editorconfig.org/>

### 关于我的 Sublime Text 配置

```
{
    "auto_complete_commit_on_tab": true,
    "bold_folder_labels": true,
    "caret_extra_bottom": 0,
    "caret_extra_top": 0,
    "caret_extra_width": 2,
    "caret_style": "smooth",
    "color_scheme": "Packages/Color Scheme - Default/IDLE.tmTheme",
    "default_encoding": "UTF-8",
    "detect_indentation": false,
    "draw_minimap_border": true,
    "font_face": "Courier New",
    "font_size": 15,
    "highlight_line": true,
    "ignored_packages":
    [
        "Vintage"
    ],
    "line_padding_bottom": 3,
    "line_padding_top": 3,
    "rulers":
    [
        100
    ],
    "save_on_focus_lost": true,
    "scroll_past_end": true,
    "show_encoding": true,
    "tab_size": 4,
    "translate_tabs_to_spaces": true,
    "trim_trailing_white_space_on_save": true,
    "update_check": false,
    "word_wrap": false
}
```


### 相关资源
<https://packagecontrol.io/>  
<https://github.com/jikeytang/sublime-text>