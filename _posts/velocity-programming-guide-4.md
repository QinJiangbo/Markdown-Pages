---
title: Velocity编程指南（四）
date: 2017-07-02 16:18:42
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/velocity-thumbnail.png
tags:
	- Velocity
	- 模板引擎
categories:
	- 开发技术
	- Java
keywords:
	- Velocity
	- 模板引擎
---
无论是哪种编程语言，都应该支持一些`函数`，VTL也不例外，只不过在Velocity中我们不叫它函数，叫它`宏（Macro）`，宏允许我们定义一系列的动作，并且重复使用它。使用的时候主要是创造一些自定义的新命令，什么命令都可以，都可以通过`#`号来表示。

### `#macro`命令

来简单看一下Velocity宏的使用吧：

``` vm
#macro ( tr )
<tr><td></td></tr>
#end
```

怎么使用的呢？`#tr()`就可以了。Velocity引擎会自动解析这个`#tr`命令，然后将相应的内容填充到模板中。我们称这为一次**宏调用（Macro Invocation）。**

### 宏结构macro
在Velocity中，一个宏的定义通常是由`#macro`命令开始，`#end`命令结束，中间一段是宏体**macro body**。我们的业务逻辑基本上都是写在宏体里面的。和函数一样，**macro**也是可以传参数的，下面我们演示一个传递两个参数的**宏定义（macro definition）**：

``` vm
#macro( tablerows $color $values )
    #foreach( $value in $values )
        <tr><td bgcolor=$color>$value</td></tr>
    #end
#end

#set( $greatlakes = ["Superior","Michigan","Huron","Erie","Ontario"] )
#set( $color = "blue" )

<table>
    #tablerows( $color $greatlakes )
</table>
```

引擎解析的结果就是：

``` bash
<table>
<tr><td bgcolor="blue">Superior</td></tr>
<tr><td bgcolor="blue">Michigan</td></tr>
<tr><td bgcolor="blue">Huron</td></tr>
<tr><td bgcolor="blue">Erie</td></tr>
<tr><td bgcolor="blue">Ontario</td></tr>
</table>
```

我们来说一说上面定义的这个宏的结构：由`#macro`命令开头，参数有`tablerows`，`$color`和`$values`。注意，`tablerows`前面没有美元符号，说明它不是变量，那么它是什么呢？`tablerows`是这个**宏（macro）**的名字，也就是说这个宏可以通过`#tablerows`来进行调用。很明显，`$color`和`$values`两个是变量，是作为参数传递进来的。

### 宏参数arguments
Velocity中宏只能够传递特定类型的参数，如下所示：

+ 引用，就是以美元符号`$`开头的东西
+ 字符串常量，比如`"hello"`，`"alibaba"`等
+ 数值常量，比如`102`, `1999`等
+ 整形范围，比如[`0..102`]，[`$start..$end`]等
+ 对象数组，比如`["a", "aa", "aaa"]`等
+ 布尔型数值，`true`or`false`

来一段：

``` vm
#macro( callme $name )
My name is $name.
#end

#callme( "Alibaba" )
```

结果是`My name is Alibaba.`宏调用成功！**注意宏的参数问题，如果传递过来的是一个方法的结果，那么这个方法会被宏重复执行。因为在宏里面，参数只是被替换到宏体里面的对应位置而已，然后才开始解析执行。**举个栗子：

``` vm
#macro ( repeat, $x )
$x, $x, $x
#end

## 假设$date.now()等于当前时间
#repeat( $date.now() )
```

结果是三个不一样的时间，原因是什么？不是把`$date.now()`的结果传递到宏体中去了吗？三个`$x`应该是一样的啊，但是在Velocity中，不是的！它只是把`$date.now()`替换到`$x`中，然后才开始执行，所以三个结果分别都不一样。**不要和Java中的函数调用搞混了！**

### 格式化issues
关于Velocity的格式化问题，有几个需要注意的地方，没有什么说必须写多行还是写一行的规矩，其实解析的结果都一样。但是我们推荐还是要标准化，格式化，这样便于代码的可读性。下面两个例子做个对比：

**好的例子**

``` vm
#set( $fruits = ["apple", "pear", "orange", "strawberry"] )
#foreach( $fruit in $fruits )
    $fruit
#end
```

**坏的例子**

``` vm
#set( $fruits = ["apple", "pear", "orange", "strawberry"] )
#foreach( $fruit in $fruits ) $fruit #end
```

很明显，第一个例子可读性非常好，第二个例子的可读性相对差很多。另外，`多余的空格`（**excess whitespace**）会被Velocity引擎移除掉。也就是说下面两段代码的效果是一样的：

``` vm
Send me
#set( $foo = ["$10 and ","a pie"] )
#foreach( $a in $foo )
$a
#end
please.

#################################

Send me
#set($foo       = ["$10 and ","a pie"])
                #foreach            ($a in $foo )$a
         #end please.
```
不过很明显下面的那种写法不是我们推荐的。

### 范围操作range operators
范围操作就是针对`[n..m]`遍历的操作，`n`和`m`必须是整数。有一些有趣的例子可以看看：

``` vm
## Range: 1 2 3 4 5
First example:
#foreach( $cnt in [1..5] )
    $cnt
#end

## Range: 2 1 0 -1 -2
Second example:
#foreach( $cnt in [2..-2] )
    $cnt
#end 

## Range: 0 1
Third example:
#set( $range = [0..1] )
#foreach( $cnt in $range )
    $cnt
#end

## Not a range (不在 #set or #foreach 命令中)
Fourth example:
[1..3]
```

输出结果：

``` bash
First example:
    1
    2
    3
    4
    5

Second example:
    2
    1
    0
    -1
    -2

Third example:
    0
    1

Fourth example:
[1..3]
```

### macro进阶
想一下下面的例子执行结果是什么？

``` vm
#macro( inner $foo )
    inner : $foo
#end

#macro( outer $foo )
    #set($bar = "outerlala")
    outer : $foo
#end

#set($bar = 'calltimelala')
#outer( "#inner($bar)" )
```

答案是`outer : inner : outerlala`。为什么不是`outer : inner : calltimelala`？这里需要注意的是宏定义里面都是参数替换，所以，`#inner($bar)`直接被替换到宏`outer`里面去了，里面又重新定义了`$bar`这个变量，所以以这个为准。再来一个：

``` vm
#macro( foo $color )
    <tr bgcolor=$color><td>Hi</td></tr>
    <tr bgcolor=$color><td>There</td></tr>
#end

#foo($bar.rowColor() )
```

说明一下，看过前面介绍的同学都会知道，这里的`$bar.rowColor()`会被重复执行两次，而不是一次。原因是`$bar.rowColor()`直接被替换到`foo`宏体里面去了。正确的写法是什么？

``` vm
#set( $color = $bar.rowColor() )
#foo($color)
```
这样`$bar.rowColor()`就不会执行多次了。

### 字符串拼接concatenation
字符串拼接主要是要注意变量与普通字符串常量的隔离。**千万不要用+号来拼接字符串，直接把字符串放在一起就好了。**比如：

``` vm
#set( $type = "staff" )
#set( $group = "Alibaba" )
He is an excellant $group$type!
```
结果是`He is an excellant Alibabastaff!`如果加一个字符串呢？

``` vm
#set( $type = "staff" )
#set( $group = "Alibaba" )
He is an excellant $groupGroup$type!
```
结果是`He is an excellant $groupGroupstaff!`原因是Velocity将`groupGroup`当做一个变量了。这个时候做一个小调整就可以啦，如下：

``` vm
#set( $type = "staff" )
#set( $group = "Alibaba" )
He is an excellant ${group}Group${type}!
```
如果怕`$group`或者是`$type`未定义导致页面上出现`$group`这样的字符串，我们推荐使用带感叹号的方式访问变量：

``` vm
#set( $type = "staff" )
#set( $group = "Alibaba" )
He is an excellant $!{group}Group$!{type}!
```

## 总结
到此为止，Velocity的VTL模板语言的语法终于讲完了，都是一些在工作中需要特别注意的点，希望大家反复琢磨琢磨，每一个例子争取自己都跑一遍。关于具体怎么用Java代码去跑一个这样的例子，我在下一章会给大家详细的介绍。