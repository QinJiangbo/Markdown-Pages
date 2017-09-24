---
title: Velocity编程指南（三）
date: 2017-06-29 23:21:49
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
前面说了Velocity的VTL语法，关于Velocity的对象，引用等有了比较清晰的认识。但是，Velocity本身有哪些强大的功能呢？本章我们就来讨论一下Velocity中有哪些非常强大的命令（directives）和操作。

## 命令directives
在Velocity中，我们重点需要了解的命令列举如下，以便于大家提前有一个简单的认识。

- `#set()`[单行命令]
- `#literal()`[多行命令]
- `#if() / #elseif() / #else`[多行命令]
- `#foreach()`[多行命令]
- `#include()`[单行命令]
- `#parse()`[单行命令]
- `#stop()`[单行命令]
- `#macro()`[单行命令]（后面一章会学习）

### `#set`命令
很简单就可以想到，`#set`命令主要是设置一个值对吧，具体点来说就是给一个引用赋值。我们直接来点例子吧：

``` vm
## 赋给一个变量值
#set( $name = "alibaba" )

## 赋给一个属性值
#set( $customer.Favourite = $name )
```

从上面的例子我们可以看到等号左边的被赋值的引用类型只能是变量引用或者是属性引用，其它的类型都无效，这和我们的Java中的赋值语法是一致的。重点是等号右边的元素可以是一下类型：

- 变量引用（Variable reference）
- 字符串常量（String literal）
- 属性引用（Property reference）
- 方法引用（Method reference）
- 数值常量（Number literal）
- 链表（List）
- 映射（Map）
- 表达式（Expression）

比如说下面的这些都是可以的：

``` vm
## 变量引用
#set( $fruit = $selectedFruit )

## 字符串常量
#set( $fruit.flavor = "sweet" )

## 属性引用
#set( $fruit.amount = $cart.total )

## 方法引用
#set( $fruit.color = $colorlist.selectColor($select) )

## 数值常量
#set( $fruit.value = 123 )

## 链表
#set( $fruit.sorts = ["Apple", "Pear", "Orange"] )

## 映射
#set( $fruit.shapes = {"Apple" : "round", "Pear" : "conical"}) 
```

**`多说一句`：如何获取Velocity中的链表List和映射Map中的值呢？以上面的例子来说，对于List，我们可以就类似于`java.util.List`的方式来取值，像这样`$fruit.sorts.get(0)`；对于Map，一样的，类似`java.util.Map`，如`$fruit.shapes.get("Apple")`。**

至于表达式嘛！我们看一看：

``` vm
#set( $value = $hello + 1 )
#set( $value = $hello - 1 )
#set( $value = $hello * $world )
#set( $value = $hello / $world )
```
加减乘除其实都是OK的。

### 给引用赋空值null
一般来说，Velocity是不允许给引用赋空值的，就是说，如果这个引用所指向的是一个非空的值，那么后面给它赋空值以后，它的值是不会变的，还是之前的值。怎样解除这个限制呢？Velocity从1.5版本以后给出了自己的方案，就是设置一个运行时属性`directive.set.null.allowed`为`true`，否则是不能设置控制的。下面给出一个简单的例子说明一下：

``` vm
#set( $result = $query.criteria("fruit") )
The result of the first query is $result

#set( $result = $query.criteria("customer") )
The result of the second query is $result
```

如果说`$query.criteria("fruit")`返回的是`apple` ，而`$query.criteria("customer")`返回的是`null`，上面的代码运行结果是什么？好好想一下：

``` vm
The result of the first query is apple

The result of the second query is apple
```

没错！你想的是正确结果！那我们下面来一个难度更高一点的：我们使用一个`#foreach`命令来遍历一个集合中所有非空的元素，如下：

``` vm
#set( $criteria = ["fruit", $name ,"customer"] )
#foreach( $criterion in $criteria )
    #set( $result = $criterion )
    #if( $result )
        $result
    #end
#end
```

说明一下，上面的代码中`$name`没有赋任何值，也就是说它为空，想一下，这段代码的运行结果会是什么？

``` vm
fruit
fruit
customer
```

为什么？因为第一个元素被访问以后`$result`的值变为了`fruit`，由于第二个元素是空的，所以`$result`的值不变，还是`fruit`，但是第三个元素的值是`customer`不为空，那么`$result`呗赋值成功。

**正确的做法应该是什么呢？怎样不重复地输出整个List中非空的元素呢？**

``` vm
#set( $criteria = ["name", "address"] )
#foreach( $criterion in $criteria )
    #set( $result = "" )
    #set( $result = $criterion )
    #if( $result != "" )
        $result
    #end
#end
```

结果就是

``` vm
fruit
customer
```

Bingo！

### 字符串常量`#literal`命令
当我们使用`#set`命令的时候，其实即使是在双引号中括起来的变量，一样的会被解析出来，就像这样：

``` vm
#set( $hello = "I LOVE" )
#set ( $world = "ALIBABA" )
#set ( $mylove = "$hello $world" )
```

结果就是`I LOVE ALIBABA`。惊不惊喜？意不意外？如果我们仅仅只是想表达`$hello $world`这个字符串呢，何解？答案是`#literal`命令和单引号。分别看一看：

**单引号**

``` vm
#set ( $name = 'hello' )
#set ( $hobby = '$name' )
我的兴趣是${hobby}.
```

结果是`我的兴趣是$name.`然后再看看`#literal`命令。

**`#literal`命令**

``` vm
#set ( $name = "hello" )
#literal()
我的名字是$name.
#end
```

结果是`我的名字是$name. `并不是`我的名字是hello. `为什么？因为在`#literal`命令体里面，所有的命令都失效了，全部都会被当做纯文本来处理。

### `#if / #elseif / #else`命令
和所有的编程语言一样，Velocity的模板语言也有自己的流程控制逻辑，通过`#if / #elseif / #else`命令来实现的。这个不用多说，给出一个demo就可以体会了：

``` vm
#set ($direction = 15)
#set ($wind = 6)

#if( $direction < 10 )
    <strong>Go North</strong>
#elseif( $direction == 10 )
    <strong>Go East</strong>
#elseif( $wind == 6 )
    <strong>Go South</strong>
#else
    <strong>Go West</strong>
#end
```
值得注意的是，如果一个引用没有被赋值，那么默认为空，在条件判断的时候为`false`。这一点和Java中的对象为空判断保持一致。

### `#foreach`命令
我们知道在Java中我们通过`foreach`循环遍历一个集合或者数组，同样的，在Velocity中，我们同样也是通过`#foreach`命令来遍历一个集合或者数组的。在Velocity中，使用`#foreach`命令的时候，第一个变量是循环遍历变量，第二个变量是需要被迭代遍历的集合或者数组等，中间使用关键字`in`来连接。使用方式比较简单：

``` vm
<ul>
    #foreach( $product in $allProducts )
        <li>$product</li>
    #end
</ul>
```

Velocity`#foreach`命令支持的Java类型是以下几种：

+ 任何数组类型
+ **java.lang.Collection**，使用`obj.iterator()`返回的迭代器遍历
+ **java.lang.Map**，使用`map.values().iterator()`返回的迭代器遍历
+ **java.lang.Iterator**，直接使用这个迭代器遍历
+ **java.lang.Enumeration**，Velocity会把枚举包装成迭代器再遍历

`注意`：不推荐直接使用`java.lang.Iterator`和`java.lang.Enumeration`来直接遍历，因为它们不支持循环多遍，迭代器循环完一遍以后指针就不在前面了，再次遍历就取不到元素了。

遍历的时候我们需要统计这个元素的次序怎么办？很简单，Velocity为我们提供了步数（`$velocityCount`）和步长（`counter`）的支持。可以在运行时配置它，如下：

``` properties
# Default name of the loop counter
# variable reference.
directive.foreach.counter.name = velocityCount

# Default starting value of the loop
# counter variable reference.
directive.foreach.counter.initial.value = 1
```

从上面可以看到，`$velocityCount`这个名字是可以被替换成任何你想要的名字的。但是，一般我们用默认的就行啦，很多Java框架解析的时候一般也是使用默认框架的。

**单次遍历**

``` vm
<table>
    #foreach( $customer in $customerList )
        <tr><td>$velocityCount</td><td>$customer.Name</td></tr>
    #end
</table>
```

**嵌套遍历**

``` vm
#foreach ($category in $allCategories)
    Category # $velocityCount is $category
    #foreach ($item in $allItems)
        Item # $velocitycount is $item
    #end
#end
```

为了避免死锁，Velocity还针对`#foreach`命令给出了一个限制的办法，就是设置一个运行时变量的值：

``` properties
# The maximum allowed number of loops.
directive.foreach.maxloops = -1
```
默认为`-1`，表示不作限制。

### 加载资源`#include`和`#parse`命令
Velocity中资源有两种方式，一种是普通文件，另一种是模板文件。针对普通文件和模板文件加载的命令也是不一样的，不能搞混了。**一般来说`#include`命令是用来加载普通文件的，而`#parse`命令被用来加载模板文件。**从命令的英文名字上也可以看出来对吧，`include`表示包括的意思，`parse`表示解析的意思。模板文件比普通文件多的是一些待解析的Velocity命令。 

**加载普通文件**

``` vm
## 加载普通文件one.txt
#include( "one.txt" )

## 加载多个文件
#include( "one.txt","two.txt","three.txt" )
```

另外，我们还可以使用引用的形式表示待加载文件。

``` vm
## 将文件one.txt赋给变量$file0
#set( $file0 = "one.txt" )

## 加载多个文件
#include( $file0,"two.txt","three.txt" )
```

**加载模板文件**

`#parse`命令和`#include`命令很相似，但是`#parse`命令只能带一个参数，不像`#include`命令能带多个参数，这点需要大家多多注意。


``` vm
## ########################################
## 模板文件 "alibaba.vm"
## ########################################
Hello, Alibaba!
#set( $name = "Alibaba" )
#parse("taobao.vm")
#parse("tmall.vm")
## ########################################


## ########################################
## 模板文件 "taobao.vm"
## ########################################
#set( $name0 = "Taobao" )
Hello, $!{name} from $!{name0}!
## ########################################

## ########################################
## 模板文件 "tmall.vm"
## ########################################
#set( $name0 = "Tmall" )
Hello, $!{name} from $!{name0}!
## ########################################
```

结果就是：

``` bash
This is a Alibaba day.
Hello, Alibaba from Taobao!
Hello, Alibaba from Tmall!
```

### `#stop`命令
在调试的时候，如果我们只想让模板被解析一部分呢？因为可能某个模板特别长，而我们需要被解析的部分可能就是开头的那么一小段，如果整个模板被解析的话可能非常耗时，这个时候我们就需要手动地告诉模板引擎不要再解析了，到此为止，后面的内容就会全部丢弃掉，不再解析。Velocity中`#stop`命令可以实现这个功能。直接来例子说话。

``` vm
#set( $name = "alibaba" )
My name is $name.
#stop
This line won't be displayed!
```

结果就是：

``` bash
My name is alibaba.
```

可以看到，下面那句`This line won't be displayed!`真的没有显示出来。但是不代表你的模板文件存在语法错误的时候它会忽略掉哈，如果说你的模板中在`#stop`命令后面的代码存在语法错误的时候，引擎依然会报错的。**两个英文单词可以区分，`#stop`命令是`stop rendering`，而不是`stop parsing`。**

### 操作符operators
操作符比较简单，大家注意一下就是文本的写法比如`and`表示**与**和简略的写法`&&`表示**与**是一样的。

``` vm
#set ($a = true)
#set ($b = false)

#if ($a || $b)
    换做or也是对的
#end

#if ($a and $b)
    换做&&也是对的
#end
```

来一张对照表格：

|Type of operator	|short version	|text version|
|----------|:----------:|:---------:|
|equal	|==	|eq|
|not equal	|!=	|ne|
|greater than	|>	|gt|
|greater or equal than	|>=	|ge|
|less than	|<	|lt|
|less or equal than	|<=	|le|
|logical and	|&&	|and|
|logical or	|&#124;&#124;	|or|
|logical not	|!	|not|

`前方高能预警`：**非常需要大家注意的是不像其它的语言那样，数字`0`和字符串空串`""`可以表示为`false`，Velocity中是不可以的，它只允许布尔型的值`false`和空值`null`转为`false`，其它一切的对象都会被当做是`true`。务必注意啦！**