---
title: Velocity编程指南（二）
date: 2017-06-21 23:46:19
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
Velocity模板引擎如何使用，语法是怎样的？本博客将会在变量的赋值，引用以及注释等三个方面来谈一谈Velocity的语法。但是在开始语法之前，我们快速地搞一个Hello World出来试下水。

``` html
<html>
<body>
#set( $name = "Velocity" )
Hello $name World!
</body>
</html>
```
可以看到这段代码经过模板引擎渲染以后会输出以下文字：

``` html
<html>
<body>
Hello Velocity World!
</body>
</html>
```
展示出来就是一个HTML页面，上面写着`Hello Velocity World!`。观察上面这个例子不难发现，`Hello $name World!`被替换为了`Hello Velocity World!`，原因是`$name`变量被`#set($name = "Velocity")`语句赋值为了`Velocity`，因此就直接等值替换了。

`小建议`：为了使你的VTL代码可读性更高，我们建议你的赋值语句最好独占一行，剩下的语句就另起一行。

## 语法元素Elements
和其他的语言一样，Velocity一样也有一些语法元素，比如`语句和命令`，`引用`以及`注释`。本节主要介绍这三个方面。

### 语句和命令
先说一下语句和命令的区别，Linux命令我们都知道一些，比如`rm`，`ls`以及`ps`等等，这一些都是命令，**shell编程中它们只是语句中的一步分，后面还会带很多参数**。Velocity中的命令一样，也是语句的一部分，下面我们就分析一下一个VTL是怎么组成的？

``` vm
#set($a = 10)           ## Velocity单行语句
^\_/\_______/
| |     |
| |     +---------- 圆括号中的表达式
| |
| +---------------- Velocity命令
|
+------------------ Velocity语句的开始标志



+------------------ Velocity语句的开始标志
|
|   +-------------- Velocity命令
|   |
|   |          +--- 圆括号中的表达式
| __|__  ______|______
v/     \/             \
#foreach($i in [0..10])   ## Velocity多行语句
    Counter is $i           ## 语句体
#end                      ## Velocity语句的结束标志
```

**单行语句例子**

``` vm
#set($name = "Banana")
#include("Apple.vm")
#parse("Apple.vm")
#stop
```

**多行语句例子**

``` vm
#foreach($i in [1..20])
    The num is $i
#end

#literal
    This is a literal block of sample text
#end

#if($fruit == "banana")
    This is a banana.
#else
    This is not a banana.
#end
```

`注意`：这里`#end`命令其实也可以和前面的语句放在同一行，但是呢，我们依然称这些语句为**多行语句**。

### 引用references
这个很有趣，因为我们都知道，在Java中我们使用标识标识符去操作很多事情，然后最后给其指定一个值，根据这个值，这些操作最后会有一个结果。我们将这个标识符称为一个**引用**（引用都是通过一个`$`符号引导的）。尤其是在Velocity做模板开发Java Web程序的时候，Web页面的设计者只需要关心页面是如何组织的，并不关心后台的数据如何执行，并且是如何传过来的，他们只需要**假想**这个模块已经有值了，然后根据这个**假想**去设计相关的功能，这样就将工作和后端的工程师隔离了，大大提高了工作效率。我们来看一看下面的例子有哪些引用？

``` vm
## 简单引用
The total amout is $total.

## 属性引用
You currently have $cart.items Items in your Shopping cart.

## 方法引用
We offer you a discount of $cart.calculateDiscount() for your purchase.

## 在Velocity中直接创建一个全新的引用
#set($linesPerPage = 20)
```

`注意`：有一种情况需要小心，那就是标识符的区分度问题，比如下面这样的：

``` vm
#set($we = "Alibaba")
This is a $weday.
```
大家可能会觉得结果是`This is a Alibabaday.`实际情况呢？`This is a $weday.`，怎么回事？不应该是`This is a Alibabaday.`吗？原因是`$we`和`$weday`都被当成一个独立的标识符了，所以，这个时候需要区分开，加上花括号即可解决这个问题。

``` vm
#set($we = "Alibaba")
This is a ${we}day.
```
结果就是：`This is a Alibabaday.`

### 注释comments
 Velocity的注释和Java的注释差不多，也分为单行注释和多行注释。单行注释一般是以两个`#`号开始的，为什么不是一个`#`号？因为一个`#`号表示一个Velocity命令啊！多行注释就是采用`#`号和`*`号结合的方式来使用的，用法和Java里面的非常相似！下面就简单地说说单行注释和多行注释。
 
**单行注释的例子**

``` vm
## 这是单行注释哟.
This is visible in the output ## 这也是单行注释哟.
```

**多行注释的例子**

``` vm
这是多行注释块外面的文本，是可见的。

#*
  我们在多行注释，放我出去！！！为什么我不可见！！！
*#

上面👆多行注释里面的文本一点都不蛋定，看我，在注释快外面，是可见的，哈哈😆！
```

还有一种方式使用多行注释，就是像Java代码一样标准化的代码块，如下：

``` vm
#**
This is a VTL comment block and
may be used to store such information
as the document author and versioning
information:
@author qinjiangbo@github.io
@version 1.0.0
*#
```

### 转义符escape
在Velocity里面，转义符的规则和Java里面其实是差不多的，下面结合几个具体的例子来说说。

``` vm
\	表示一个反斜杠
\\	表示一个反斜杠
\#	（没有命令跟着时）表示一个\#
\$	（没有引用跟着时）表示一个\$
\#end	表示#end
\#set ($name = "Alibaba")	表示#set ($name = "Alibaba")
\$alibaba	表示$alibaba
```

## 引用References
开始讲一个大的方向就是引用，我们知道，正是有了引用，编程才开始变得灵活多变。在VTL里面，有三种类型的引用，分别是`变量`，`属性`和`方法`。它们是连接你的Java应用和模板的胶水，只有通过它们，我们的后台逻辑才和模板联系起来，才能构成一个完整的业务。

### 标识符identifiers
Velocity的标识符很有意思，就是只能使用字母开头的短语作为标识符的表示形式，另外命名还有以下几点要求（主要是三种类型的字符构成）：

+ 字母（a-z, A-Z）
+ 数字（0-9）
+ 横线（-）或者下划线（\_）

Velocity的标识符命名和Java一样，也是大小写敏感的，这一点非常重要！！！

### 变量variables
变量是最简单的引用类型，因为每一个引用同样也是一个变量。每一个变量代表了一个Java对象。下面来看一下有效的变量命名：

``` vm
$foo
$bar
$foo-bar
$foo_bar
$fooBar1
```

说一下一个叫`上下文`的东西，在Velocity里面，上下文通常用于存放变量和引用的映射关系。这就是为什么我们能从Java代码中直接将值传递到Velocity模板引擎中。当然啦，我们也可以直接通过Velocity命令将变量放入`上下文`中。比如下面这样：

``` vm
#set ($love = "Alibaba")
My love is $love. ## My love is Alibaba.
```
整个上下文中就包含了`$love`这个变量，因此我们可以在模板的任何地方使用它，直接等只替换就OK了。

### 属性properties
Velocity允许你通过很简单的方式去访问一个对象的属性，一般来说，我们是通过`.`号来访问一个Velocity对象的属性的。如下：

``` vm
$customer.address
$purchase.total
$cart.customerDiscount
```

一个属性（property）名字并不仅仅是代表这个对象的一个域（field），它还可以表示这个对象的方法。我们以第2个对象为例：`$purchase.total`有以下一些含义：

+ 当这个对象有`gettotal()`这个方法的时候，就会调用这个方法；
+ 如果这个对象是一个Java Bean对象，那么就会调用这个对象的getter方法`getTotal()`；
+ 如果这个对象包含get方法，就会调用这个`get()`方法，只不过total作为参数传递进去；
+ 如果这个对象包含`isTotal()`方法，调用这个方法；

好，知道了上述的规则以后，我们总结一下Velocity对象查找属性的规则：

**小写开头的属性**

``` vm
gettotal()
getTotal()
get("total")
isTotal()
```

**大写开头的属性**

``` vm
getTotal()
gettotal()
get("total")
isTotal()
```

可以看到，大写和小写前两个顺序是完全不一样的，因此，在编写代码的时候命名格式且其要规范统一，否则会碰到这种特别头疼的问题。

### 方法methods
Velocity对象的方法的调用其实非常简单，如果你知道Java方法调用的话（你一定知道，因为这是Java模板引擎），Velocity对象的方法调用和Java的方式一样。直接上例子：

``` vm
$cart.calculateTotal()
$customer.updatePurchases($cart)
$shop.checkInventory()
$cart.addTaxes(8, 16)

# Property access
$page.setTitle( "My Home Page" )
$customer.getAddress()
$purchase.getTotal()
```

属性和方法的主要不同点在于你可以给方法指定一组参数列表。因为方法是可以传递参数的，而属性没法传递。

### 引用references
在使用模板引擎的时候，需要将模板的文本和引擎的引用区分开，前面讲过，一不小心很容易变量就混合在一起了，不是特别好区分。通常我们建议使用花括号`{`和`}`跟在`$`符号后面来包含这个引用，用于区分一个引用和普通模板文本。

``` vm
${fruit}
${customer.address}
${purchase.getTotal()}
```

以第一行为例，如果你要点一杯果汁，你可能这么声明：

``` vm
I want a cup of $fruitJuice.
```

结果你啥也没得到，更别说果汁了，如果你改变一下会更好：

``` vm
I want a cup of ${fruit}Juice.
```
因为引用是`${fruit}`而不是`${fruitjuice}`，这个很重要。另外需要注意的一个地方是引用的表达上面，尤其是在Web页面模板上的时候。往往空指针的情况容易导致这个变量直接就输出在了屏幕上，我们可以换一种写法：

**之前的写法**

``` vm
<input type="text" name="email" value="$email"/>
```
如果`$email`为空怎么办？这个文本域就会显示`“$email”`这个字符串。是不是非常不友好，我们期望的是如果`$email`为空，直接不要显示任何东西。可以在`$`符号和变量之间加上一个`!`号，就像这样：

**现在的写法**

``` vm
<input type="text" name="email" value="$!email"/>
```

更规范的写法如下：

``` vm
<input type="text" name="email" value="$!{email}"/>
```

## 总结
本博客主要是讲述了Velocity模板引擎中的引用部分以及它相应的使用注意事项。Velocity中的对象引用很大程度上和Java中的是一样的，大家注意其中的几点不同就行啦。下一章主要是介绍VTL中的命令这一块。