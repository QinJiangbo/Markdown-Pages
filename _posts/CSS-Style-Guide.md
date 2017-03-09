---
title: （译）CSS编程风格指南
date: 2016-08-29 23:43:42
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/css3-thumbnail.jpg
tags:
	- CSS
	- code style
categories:
	- 开发技术
	- 前端
keywords:
---
这个页面简单地说明了jQuery各个项目中CSS编程的风格。

## 静态检测
使用CSSLint来检测错误以及潜在的问题。大多数jQuery的项目都有一个Grunt构建任务以用于静态检测所有的CSS文件： `grunt csslint`。关于CSSLint的配置项保存在`.csslintrc`文件中。

每一个`.csslintrc`文件都遵循一个特定的格式。所有的配置项都必须按字母排序并且分好组：

``` json
{
    "common1": true,
    "common2": true,
 
    "repo-specific1": true,
    "repo-specific2": true
}
```

下面是所有使用css的项目一定会使用的通用配置项：

``` json
{
    "adjoining-classes": false,
    "box-model": false,
    "box-sizing": false,
    "compatible-vendor-prefixes": false,
    "duplicate-background-images": false,
    "import": false,
    "important": true,
    "outline-none": false,
    "overqualified-elements": false,
    "text-indent": false
}
```

## 空格
通常来讲，jQuery的编程风格提倡代码具有一定的空格以提高代码的可读性。代码的最小化处理主要是为了使浏览器更加容易读取和处理而优化的。

- 使用tab键进行缩进。
- 一个空行或者一行的最后不能有空格。
- 在声明变量后，在变量后面的分号后面加上一个空格，但是注意一定不能在空格前面加上分号。
- 在声明规则的时候放置一个空格在`{`号前面。
- 规则声明完以后将结束括号放置在新的一行。
- 在相邻两个规则中间插入一个空白行以保证代码的可读性。
- 当有多个选择器共用同一个规则时候，采用逗号隔开，并且每个选择器单独起一行。
- 唯一需要缩进选择器的时候就是在嵌套的规则里面，比如说媒体查询(media queries)。
- CSS规则的属性应该只使用一个tab键进行缩进。
- URL中括号“（）”里面没有空白。

``` css
/* Bad CSS */
     .bad-spacing,.bad-example{color:#222222;background:url( "../bad.png" );}
 
         .bad-spacing{font-style:italic}
 
@media only all{
 
.media-example{
font-weight:strong;
}
 
}
 
/* Good CSS */
.good-spacing,
.good-example {
    color: #222;
    background: url("../good.png");
}
 
.good-spacing {
    font-style: italic;
}
 
@media only all {
    .media-example {
        font-weight: strong;
    }
}
```

## 格式化
- 使用十六进制颜色代码(`#5c8fb3`) 而不是使用 `rgba()`。
- 可能的话，使用十六进制值简写。
- 当使用十六进制值的时候采用小写字母。
- 使用`em`而不是`px`，除非避免不了了。
- 不要为0指定单位。
- 避免使用超过三个选择器长度的级联选择器。
- 避免不必要的级联选择器。
- 可能的话使用属性的简写。
	- 避免使用像`padding-left: 10px; padding-right: 10px; padding-top: 20px; padding-bottom: 5px;`这样的表达，可以写成使用这种`padding: 20px 10px 5px 10px;`。但是如果你只是改变了左边距，其实更适合使用padding-left这个属性。
- 不要在十进制值前面加上多余的0。
- 使用双引号而不是单引号。
- 永远记住在每一个申明后面加上一个分号。
- 除非必用不可，否则尽量少使用`!important`。

``` css
/* 好的CSS示例*/
.good-table thead th {
    color: #fff;
    background: #ffc0cb url("../images/polka-dots.png");
    font-size: 1em;
    padding: .5em 0;
}
 
.good-table thead th.selected {
    padding-left: 1em;
}
```

## 导入
- 不要在已发布的文件里面使用`@import`。因为这个更慢，他需要加载外部的文件进入这个CSS文件，而且还可能导致一些隐藏的问题。
- 不要在一个单独的CSS文件里面使用超过31个`@imports`， 否则它会使IE崩掉。
- 不要嵌套使用`@imports`，因为你无法保证他们实际的加载顺序。

## 注释
注释永远紧跟着一个空白行。在注释文本和注释开始标志之间总是需要隔着一个空格。

当使用多行注释的时候，需要在每一行的开头使用`*`。每一行的注释文本和这个`*`之间必须保持一个空格。

``` css
/* 好的单行注释示例 */
 
/*
* 好的多行注释示例
* 如果你需要使用多行注释，请这么做！
*/
```

## 类名
- 类名应该全部使用小写。
- 类名各个单词之间应该使用横杆`-`连接。
- 避免过度或者任意地使用类名的缩写。比如`.ui-button`一看就知道是用来作用于按钮，而`.b`没有任何意义。
- 选择器的使用上尽量多使用类，而不是编号ID。

``` css
/* 坏的例子 */
.uiButton,
.ui_button,
#uiButton,
.b {
 
}
 
/* 好的例子 */
.ui-button {
 
}
```

`译文原文地址：`[http://contribute.jquery.org/style-guide/css/](http://contribute.jquery.org/style-guide/css/)