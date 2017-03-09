---
title: Java泛型中E、T、K、V等的含义
date: 2016-04-08 21:28:09
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/java8-2-thumbnail.png
tags:
	- JDK8
categories:
	- 开发技术
	- Java
keywords:
	- JDK8
---
### Java泛型中的标记符含义：

 **E** - Element (在集合中使用，因为集合中存放的是元素)

 **T** - Type（Java 类）

 **K** - Key（键）

 **V** - Value（值）

 **N** - Number（数值类型）

**?** -  表示不确定的java类型

 **S、U、V** - 2nd、3rd、4th types

Object跟这些标记符代表的java类型有啥区别呢？

Object是所有类的根类，任何类的对象都可以设置给该Object引用变量，使用的时候可能需要类型强制转换，但是用使用了泛型**T、E**等这些标识符后，在实际用之前类型就已经确定了，不需要再进行类型强制转换。