---
title: Java编程思想之操作符（三）
date: 2017-03-22 13:06:19
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/ThinkingInJava.png
tags:
	- Java
	- 编程思想
categories:
	- 开发技术
	- Java
keywords:
	- Java
	- 编程思想
---
本文将的操作符就不像书中那样描述啦，博主总结一些比较重要而且比较容易出错的点。这些点弄明白了操作符这一块才能真正地搞清楚。

## “+”操作符
有人说**“+”**这个操作符也需要讲？Naive！如果只是简单地描述`1+1=2`，那这个篇文章就没有啥存在的意义了。这里主要描述的是与字符串相关的操作。

### str1 + str2
Java为我们做了一件很重要的事情就是让`String`支持`+`操作符。这样做有利也有弊。
首先说说好处吧，就是我们的代码会变得很简洁，而且更加利于理解。比如要描述一件商品信息，我们会重写`toString()`方法。

``` java
public String toStrinig() {
	return this.name + " - $" + this.price + " - " + this.addr;
}
```
这里会返回商品的格式化信息，看起来代码很简洁。但是这么做有什么问题呢？**我们必须得注意到`String`是`final`修饰的，意味着String类型是不可变的，那么两个字符串相加`+`的结果就是重新创建一个新的字符串，这个字符串由这两个字符串拼接而成。**那么返回了新的字符串以后原来的字符串对象哪儿去了？等死呗，等待着GC来回收。显而易见，这样会很浪费空间，**而且效率很低，因为会不断地创建对象以及销毁对象。**比较Nice的做法就是使用`StringBuilder`或者`StringBuffer`对象，他们不需要多次创建对象，效率会高一些！

``` java
public String toString() {
	StringBuilder stringBuilder = new StringBuilder();
	stringBuilder.append(this.name).append(" - $")
		.append(this.price).append(" - ").append(this.addr);
	return stringBuilder.toString();
}
```
当然了，这种方式看起来可读性就不如前面的`+`操作符了。这二者的权衡靠开发者衡量了，如果性能要求没有那么高就使用`+`吧，更可读。如果性能有要求，最后用后面的方式。

### num + str
之前看到了很简单的一道题，就是询问下面的代码输出：

``` java
String s = 1 + 2 + 5 + "$" + 1;
System.out.println(s);
```
那么s到底会变成啥呢？`8$1`。为什么不是大家想的`125$1`呢？**那是因为在Java中，字符串和数字相加的时候会发生一个类型转换的问题，会自动地将数字转化为字符串。但是！由于`+`是一个从左到右的操作符，所以，根据上下文环境来看，如果相加的两个都是数字，则执行普通的相加操作，如果是一个数字和字符串，则执行字符串的连接操作。**

## 别名问题
Java通过引用的方式访问对象，那么如果两个引用同时指向同一个对象呢？这个就是我们说的**别名现象**。如下：

``` java
int[] a = new int[5];
int[] b = new int[5];
int[] c = a;
```
可以看到，其实只创建了两个数组而不是三个数组，其中`a`和`c`表示的是同一个数组，`b`单独表示一个数组。对于`a`和`c`，不管哪一个改变了数组的元素，通过另个引用去访问数组元素都会和之前不同。这是一个很容易犯的错误。

``` java
// People(name, age, hobby)
// toString() { return name + "-" + age + "-" + hobby;  }
People p = new People("Richard", 24, "Girl"); 
// method
public void chgName(People p) {
	p = new People("Jimmy", 24, "Girl");
}
System.out.println(p);
```
那么这个时候`p`到底变成了什么呢？不少同学说`p`的名字已经变成了`Jimmy`，所以会打印`Jimmy-24-Girl`。恭喜你！成功避开正确选项。正确答案还是`Richard-24-Girl`。为什么会这样呢？我们注意到在`chgName`方法里面，局部变量`p`在调用的时候被初始化为`Richard-24-Girl`的这个对象，但是在方法体里面，这个局部变量`p`又被重新赋了一个新的对象`Jimmy-24-Girl`。这个时候我们知道这个局部变量和原来的那个对象便没有的什么关系，所以原来的对象并不会发生改变。可以见下图：

![](https://obrxbqjbi.qnssl.com/blog/image/string-alias.png)

## 自增和自减
自增`++`和自减`--`这个其实不难理解，无非就是将自身的值加一或者减一呗。但是这里一旦和变量的赋值操作结合起来就比较有意思了。

``` java
int a = 7;
int b = a++;
int c = ++a;
```
`a, b, c`的结果应该会是什么呢？有人可能会回答`9, 8, 9`。恭喜你，又答错了！正确的答案是`9, 7, 9`。为啥呢？我们注意到`int b = a++;`这一句是针对变量`b`赋值，但是需要注意这个赋值的顺序，是先将`a`赋值给`b`，然后`a++`， 因此`b`为7。变量`c`又是什么情况呢？注意到`int c = ++a;`在赋值语句右边紧跟着的是`++`自增语句，因此`a`会先自增为9，然后将`a`的值赋给`c`，所以`c`的取值是9。

自减的情况是一样一样的，这个地方很容易出现问题，如果你还不知道的话就赶紧好好敲一遍代码，理解并巩固一下。