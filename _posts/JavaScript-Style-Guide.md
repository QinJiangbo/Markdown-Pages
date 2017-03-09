---
title: （译）JavaScript编程风格指南
date: 2016-08-29 23:43:13
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/javascript-thumbnail.jpg
tags:
	- JavaScript
	- code style
categories:
	- 开发技术
	- 前端
keywords:
---
## 静态检测
使用JSHint来检测错误以及潜在的问题。大多数jQuery的项目都有一个Grunt构建任务以用于静态检测所有的CSS文件： `grunt jslint`。关于CSSLint的配置项保存在`.jslintrc`文件中。

每一个`.jslintrc`文件都遵循一个特定的格式。所有的配置项都必须按字母排序并且分好组：

``` json
{
    "common1": true,
    "common2": true,
 
    "repoSpecific1": true,
    "repoSpecific2": true,
 
    "globals": {
        "repoGlobal1": true,
        "repoGlobal2": false
    }
}
```

下面是所有使用javascript的项目一定会使用的通用配置项：

``` json
{
    "boss": true,
    "curly": true,
    "eqeqeq": true,
    "eqnull": true,
    "expr": true,
    "immed": true,
    "noarg": true,
    "quotmark": "double",
    "smarttabs": true,
    "trailing": true,
    "undef": true,
    "unused": true
}
```
*如果这个项目支持一个没有实现ES5标准的浏览器，则`es3`这个选项必须在repo-specific选项中被指定*

## 空格
通常来讲，jQuery的编程风格提倡代码具有一定的空格以提高代码的可读性。代码的最小化处理主要是为了使浏览器更加容易读取和处理而优化的。

- 使用tab键来缩进。
- 一个空行或者一行的最后不能有空格。
- 每一行最好不要超过80个字符的长度，并且一定不能超过100个字符的长度（一个tab键认定为4个空格）。有两个特殊情况，每个都允许某一行超过100个字符的长度：
	- 如果这一行包括了一个拥有很长URL的注释。
	- 如果这一行包括了一个正则表达式。这个时候正则表达式不得不使用正则表达式的构造器，需要的话还需要加上其它特殊字符转义字符。
- `if`/`else`/`for`/`while`/`try` 永远要有括号包裹着并且永远保持多行。
- 一元特殊字符的操作符（比如 `!`, `++`）后面必须要紧跟着一个空格。
- 任何`,`和`;`前面不能有空格。
- 任何被用作一个声明语句的终止符的`;`都需要在行尾。
- 一个对象中的任何属性名称前面的冒号`:`前面都不需要加上空格。
- 在三元表达式中`?`和`:`前后都必须加上空格。
- 在空的构造函数内部不能加上空格。（比如`{}`, `[]`, `fn()）
- 每个文件末尾都需要加上新的一行。
- 如果整个文件都是在一个闭包里面，那么这个函数体是不需要缩进的。

## 坏的示例

``` javascript
// Bad
if(condition) doSomething();
while(!condition) iterating++;
for(var i=0;i<100;i++) object[array[i]] = someFn(i);
```

## 好的示例

``` javascript
var i = 0;
 
if ( condition ) {
    doSomething();
} else if ( otherCondition ) {
    somethingElse();
} else {
    otherThing();
}
 
while ( !condition ) {
    iterating++;
}
 
for ( ; i < 100; i++ ) {
    object[ array[ i ] ] = someFn( i );
}
 
try {
 
    // Expressions
} catch ( e ) {
 
    // Expressions
}
 
array = [ "*" ];
 
array = [ a, b ];
 
foo( arg );
 
foo( options, object[ property ] );
 
foo( [ a, b ], "property", { c: d } );
 
foo( { a: "alpha", b: "beta" } );
 
foo( [ a, b ] );
 
foo( {
    a: "alpha",
    b: "beta"
} );
 
foo( function() {
 
    // Do stuff
}, options );
 
foo( data, function() {
 
    // Do stuff
} );
```

## 对象和数组表达式

如果对象和数组表达式很短，它们是可以占用一行而不是多行的（同时也记住这个每一行长度的限制）。当一个表达式太长以致于没法适应一行的时候，则每一行必须要有一个属性或者元素，在对应的行里面使用开闭括号将它们包裹起来。只有当属性的名字是关键字的时候才需要将其加上引号:

``` javascript
// Bad
map = { ready: 9,
    when: 4, "you are": 15 };
 
array = [ 9,
    4,
    15 ];
 
array = [ {
    key: val
} ];
 
array = [ {
    key: val
}, {
    key2: val2
} ];
 
// Good
map = { ready: 9, when: 4, "you are": 15 };
 
array = [ 9, 4, 15 ];
 
array = [ { key: val } ];
 
array = [ { key: val }, { key2: val2 } ];
 
array = [
    { key: val },
    { key2: val2 }
];
 
// Good as well
map = {
    ready: 9,
    when: 4,
    "you are": 15
};
 
array = [
    9,
    4,
    15
];
 
array = [
    {
        key: val
    }
];
 
array = [
    {
        key: val
    },
    {
        key2: val2
    }
];
```

## 多行声明语句

当一个声明语句太长以致于无法单独在一行显示的时候，换行符必须在操作符后面出现。

``` javascript
// Bad
var html = "<p>The sum of " + a + " and " + b + " plus " + c
    + " is " + ( a + b + c );
 
// Good
var html = "<p>The sum of " + a + " and " + b + " plus " + c +
    " is " + ( a + b + c );
```
 
每一行都需要根据一定的逻辑分配到某一组，比如说讲一个三元表达式的各个成分分开到单独一行可以这么做：

``` javascript
var baz = firstCondition( foo ) && secondCondition( bar ) ?
    qux( foo, bar ) :
    foo;
```

当一个判断条件语句太长以致于不能适应某一行的时候，后续的行必须多加上一个缩进以便于区分语句体和这个判断条件语句。

``` javascript
    if ( firstCondition() && secondCondition() &&
            thirdCondition() ) {
        doStuff();
    }
```

## 链式方法调用

当一个方法调用链太长的时候，必须保持每一行一个调用，并且第一个调用方法和这个调用的对象在单独一行。当方法改变了上下文的时候，必须使用不同等级的缩进以区分。

``` javascript
elements
    .addClass( "foo" )
    .children()
        .html( "hello" )
    .end()
    .appendTo( "body" );
```

## 全文件闭包

当整个文件被包裹在一个闭包里面的时候，这个闭包体是不需要使用缩进的。

``` javascript
( function( $ ) {
 
// This doesn't get indented
 
} )( jQuery );
```

``` javascript
module.exports = function( grunt ) {
 
// This doesn't get indented
 
};
```

这同样也适用于AMD包裹器：

``` javascript
define( [
    "foo",
    "bar",
    "baz"
], function( foo, bar, baz ) {
 
// This doesn't get indented
 
} );
```
对于UMD，这个工厂（factory）使用缩进就是用来在视觉上区分它和这个闭包体。

``` javascript
( function( factory ) {
    if ( typeof define === "function" && define.amd ) {
 
        // AMD. Register as an anonymous module.
        define( [
            "jquery"
        ], factory );
    } else {
 
        // Browser globals
        factory( jQuery );
    }
}( function( $ ) {
 
// This doesn't get indented
 
} ) );
```
## 构造函数
构造函数永远都需要带参调用，即使参数为空。

``` javascript
throw new Error();
when = time || new Date();
```
当一个属性或者方法立马被一个刚声明好的构造函数调用时，注意显式地加上括号。

``` javascript
detachedMode = ( new TemplateFactory( settings ) ).nodeType === 11;
match = ( new RegExp( pattern ) ).exec( input );
```
## 相等性
严格的相等性检查（===）必须优于抽象的相等性检查（==）。唯一的异常就是检查一个undefined对象或者是一个空对象。`== null`的使用在只有出现逻辑上的为空或者undefined空对象时，比如未初始化的变量。

``` javascript
// Check for both undefined and null values, for some important reason.
undefOrNull == null;
```

## 类型检查
- String: typeof object === "string"
- Number: typeof object === "number"
- Boolean: typeof object === "boolean"
- Object: typeof object === "object"
- Plain Object: jQuery.isPlainObject( object )
- Function: jQuery.isFunction( object )
- Array: jQuery.isArray( object )
- Element: object.nodeType
- null: object === null
- null or undefined: object == null
- undefined:
- Global Variables: typeof variable === "undefined"
- Local Variables: variable === undefined
- Properties: object.prop === undefined

## 注释
注释永远紧跟着一个空白行。注释的第一个首字母应该大写，如果是中文就没这个要求。但是不需要你在一行就将这句话写完，可以分多行写。在这个注释符号和注释文本之间必须要加上一个空格。

单行注释通常加在它们所注释的代码上面一行：

``` javascript
// We need an explicit "bar", because later in the code foo is checked.
var foo = "bar";
 
// Even long comments that span
// multiple lines use the single
// line comment form.
```

多行注释仅仅被用作文件的注释和函数声明开头的注释。

作为一种特殊情况，当需要注释某个参数或者需要支持一个特定的开发工具的时候内联注释是被允许的。

``` javascript
function foo( types, selector, data, fn, /* INTERNAL */ one ) {
 
    // Do stuff
}
```
不要在注释中写API文档。API文档应该单独的生成一份。（译者：貌似这点Java不太赞同）

## 引号
jQuery支持双引号。

``` javascript
var double = "I am wrapped in double quotes";
```

字符串中如果想使用内联的引号必须是外面双引号里面单引号。

``` javascript
var html = "<div id='my-id'></div>";
```

## 分号
使用它们，决不依赖ASI。

## 命名传统
变量和函数的命名应该是全名称的，采用驼峰命名法，出第一个单词小写外，其它每个单词都大写。名字应该是能够描述这个变量和函数的用途的。迭代的变量除外，因为迭代器的i没意义。

## 全局变量
每个项目推荐最多只能暴露一个全局变量。

## DOM结点规则
`.nodeName` 永远优于 `.tagName`使用。

`.nodeType` 应该被用来指定结点的类型 (而不是 `.nodeName`)。

## Switch语句
`switch`语句的用法通常来讲是不提倡的。但是在分类情形很多的时候还是很有帮助的。特别是当多个情况可以被一个代码块处理的时候，或者是设定整体的默认的逻辑`default`。

当使用switch语句的时候：

- 除了`default`情况，其它每一个情况都需要使用`break`。
- 每个`case`语句都需要和`switch`对齐。

``` javascript
switch ( event.keyCode ) {
case $.ui.keyCode.ENTER:
case $.ui.keyCode.SPACE:
    x();
    break;
case $.ui.keyCode.ESCAPE:
    y();
    break;
default:
    z();
}
```

`译文原文地址：`[http://contribute.jquery.org/style-guide/js/](http://contribute.jquery.org/style-guide/js/)