---
title: （译）HTML编程风格指南
date: 2016-09-10 23:43:29
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/html5-thumbnail.jpg
tags:
	- HTML
	- code style
categories:
	- 开发技术
	- 前端
keywords:
---
这个页面简单地说明了jQuery各个项目中HTML编程的风格。

## 静态检测
使用grunt-html来检测错误以及潜在的问题。大多数jQuery的项目都有一个Grunt构建任务以用于静态检测所有的CSS文件： `grunt htmllint`。

## 空格
通常来讲，jQuery的编程风格提倡代码具有一定的空格以提高代码的可读性。

- 使用tab键进行缩进。
- 不要直接使用`html`, `body`, `script`, 或者`style`等元素的子节点进行缩进。使用其他任意的东西缩进都可。
- 一个空行或者一行的最后不能有空格。
- 通过空格来分隔元素的各个属性。

``` html
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>Sample Page</title>
 
    <link rel="stylesheet" href="/style.css">
    <style>
    body {
        font-size: 100em;
    }
    </style>
 
    <script src="/jquery.js"></script>
    <script>
    $( function() {
        $( "p" ).text( $.fn.jquery );
    } );
    </script>
</head>
<body>
 
<section>
    <p>jQuery is awesome!<p>
</section>
 
</body>
</html>
```

## 格式化
- 永远使用最小化的，最小版本号的并且是小写的doctype。
- 永远指定这个页面写入的自然语言类型。
- 永远包含`html`, `head`, 以及 `body`这几个标签。
- 永远指定编码类型。大多数的网页使用的基本上都是UTF-8。这个编码类型越早指定越好，而且必须在这个网页文档的钱1024个字节内容前。
- 元素的名称和属性的名字永远使用小写。
- 属性的值永远加上引号。使用双引号而不是单引号。
- 包含可选择的结束标签。
- 自闭合的元素不需要加上结束标签。
- 选择性的属性应该被省略掉。
- `rel`, `type`, `src`, `href` 以及 `class`等属性必须在其它任何元素前声明。 
- 永远为一个图片元素加上一个alt属性。

``` html
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>Good Example</title>
    <link rel="stylesheet" href="style.css">
    <script src="/jquery.js"></script>
</head>
<body>
 
<img class="sample-image" src="assets/img/img.png" alt="Sample Image">
<p>This is a sample image</p>
 
</body>
</html>
```

## HTML语义
永远恰当地使用HTML元素。

## 减少封装
永远需要避免多余的父元素结点。

``` html
<!-- Bad HTML -->
<span class="avatar">
    <img src="assets/img/img.png" alt="Jane Doe">
</span>
 
<!-- Good HTML -->
<img class="avatar" src="assets/img/img.png" alt="Jane Doe">
```

## 注意分隔
永远保持标记，样式文件和脚本文件独立成行分隔。

``` html
<!-- Bad Example -->
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>Sample Page</title>
    <script src="script.js"></script>
</head>
<body>
 
<h1 style="font-size:1em;line-height:2em;">This is a heading.</h1>
<p style="text-decoration:underline;font-size:.5em;line-height:1em;">This is an underlined paragraph.</p>
</body>
</html>
 
<!-- Good Example -->
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <title>Sample Page</title>
    <link rel="stylesheet" href="style.css">
    <script src="script.js"></script>
</head>
<body>
 
<h1>This is a heading.</h1>
<p>This is an underlined paragraph.</p>
</body>
</html>
```
## 表单
对于标记元素要加上一个for属性。在跨浏览器和及其先关辅助技术中这个比隐式地指定标记的对象更加健壮。

``` html
<!-- Bad Example -->
<label><input type="radio" name="input" value="first"> First</label>
 
<!-- Good Example -->
<input type="radio" name="input" id="input-1" value="first">
<label for="input-1">First</label>
```

对于标记不要使用placeholder属性。切记使用label元素。

``` html
<!-- Bad Example -->
<input type="text" id="name" placeholder="Enter your name">
 
<!-- Good Example -->
<label for="name">Name</label>
<input id="name" type="text" placeholder="Jane Doe">
```

## 注释
注释永远紧跟着一个空白行。在注释文本和注释开始标志之间总是需要隔着一个空格。

当使用多行注释的时候，需要在每一行的开头使用`<!--`。每一行的注释文本和这个`-->`之间必须保持一个空格。

``` html
<!-- Good single line comment -->
 
<!--
Good example of a multi-line comment.
If your comment takes up multiple lines, please do this.
-->
```

`译文原文地址：`[http://contribute.jquery.org/style-guide/html/](http://contribute.jquery.org/style-guide/html/)