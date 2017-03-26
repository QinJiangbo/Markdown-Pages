---
title: Java编程思想之方法重载（四）
date: 2017-03-22 14:51:23
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
如果你能在十五秒内答出如何区分两个函数如何区别的问题，那么本文你就可以直接略过了。如果不能的话，或者不是很确切的话，还是听我唠叨唠叨一下吧。

## 方法重载
什么是方法？我们都知道当在堆上创建一个对象的时候，我们也就给堆上的这片内存空间取了一个名称。那么方法是什么呢？所谓方法就是给某一个动作取的名字。我们通常都是通过某一个方法执行某一段具体的动作。通过使用名字，你可以访问所有的对象和方法（匿名的除外哈）。

我们说一个好的名字不仅能够愉悦身心，而且还能提升代码的可读性和可维护性！所以，取一个好名字很重要，但是如果某个名字太火了，大家执行某个相似的业务逻辑时都想用怎么办？这个好办啊。使用方法重载。

## 如何区分不同的方法
一句话，**每一个方法都必须有一个独一无二的参数类型列表。**稍加思考就会觉得这句话说得有道理，因为如果几个重载函数的参数类型都不能区分这些方法了，还有什么能区分这些方法呢？返回值么？？？有人肯定会这么问。好我现在给一个例子：

``` java
public void count(int a) {
	a++;
}

public int count(int a) {
	return a++;
}
```
是啊，这么一看还真是两个不一样的方法！但是需要注意，这么写在Java中是编译不通过的。原因很简单，如果我这么调用这个函数：

``` java
count(1);
```
你跟我说说，我调用的是哪个方法？没法知道了吧。所以，返回值的区分方式是不可行的。还是得通过参数类型列表来区分。甚至连参数的顺序都能有效区分各个重载方法。但是我们通常不这么推荐，因为会造成代码的可读性大大降低，从而影响代码的可维护性。下面给一个正例：

``` java
public int count(int a) {
	return a++;
}

public double count(double a) {
	return a++;
}

public float count(float a) {
	return a++;
}
```
这样我们就能知道相关的重载方法了。

## 数据的窄化转化
对于基本数据类型作为参数的方法重载，我们需要注意一个问题就是**如果传入的数据类型（实参）小于方法的形式参数类型（形参），那么，实参的类型就会被自动的提升。与此同时，如果实参的类型比形参的类型大，则实参必须通过类型转换（cast）来实现窄化，否则编译不通过。**

### 实参类型小于形参类型

``` java
void f1(int a){ System.out.println("f1 int"); }
void f1(char a){ System.out.println("f1 char"); }
void f1(double a){ System.out.println("f1 double"); }
void f1(float a){ System.out.println("f1 float"); }

void f2(long a){ System.out.println("f2 long"); }
void f2(double a){ System.out.println("f2 double"); }
```
测试：

``` java
int x = 10;
f1(x); f2(x);
```
结果会是什么？

``` java
f1 int
f2 long
```
因为`f2`里面没有`int`类型的形参，所以只能讲x提升为`long`型。

### 实参类型大于形参类型
另一个方面，看看窄化转化的情况：

``` java
void f3(long a){ System.out.println("f3 long"); }
void f3(double a){ System.out.println("f3 double"); }

void f4(int a){ System.out.println("f4 int"); }
```
这个时候使用`long`型来测试：

``` java
long x = 17l;
f3(x); f4(x);
```
会发生什么问题？我们可以看到`f4(x)`编译不通过，这是为什么？因为`f4`只有`int`型的参数，必须降低`x`的类型才能调用，所以就应该这么改进：

``` java
f4((int) x);
```
这样就OK了，但是也需要注意会发生一些数据丢失的情况，因为是从高数据类型转化为低数据类型的。