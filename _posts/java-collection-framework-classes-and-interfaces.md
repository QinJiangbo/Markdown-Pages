---
title: （译）Java集合框架类和接口层级图
date: 2016-12-25 20:47:01
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/java8-2-thumbnail.png
tags:
	- JDK8
	- 集合框架
categories:
	- 开发技术
	- Java
keywords:
	- JDK8
	- 集合框架
---
## 1. Collection vs Collections
首先，"Collection"和"Collections"是两个不同的概念。从下面的层级图你可以看到，"Collection"是集合框架层级图的根元素，但是"Collections"只是一个提供操作集合框架静态方法的工具类。

![](https://obrxbqjbi.qnssl.com/blog/image/CollectionsHierarchy.png)

## 2. 集合框架类层级图
下面一幅图显示了集合框架的类层级图。

![](https://obrxbqjbi.qnssl.com/blog/image/java-collection-hierachy.png)

## 3. Map映射的类层级图
这是Map映射的类层级图。

![](https://obrxbqjbi.qnssl.com/blog/image/MapClassHierarchy.png)

## 4. 各个类汇总
|Interfaces|Hash table|Resizable array| Tree|Linked list |Hash table + Linked list|
|-----------|----------|--------|---------|----------|-------|
|Set|HashSet||TreeSet||LinkedHashSet|
|List||ArrayList||LinkedList||
|Queue||||||
|Map|HashMap||TreeMap||LinkedHashMap|

## 5. 代码示例
下面这段代码阐释了不同的集合类型。

``` java
List<String> a1 = new ArrayList<String>();
a1.add("Program");
a1.add("Creek");
a1.add("Java");
a1.add("Java");
System.out.println("ArrayList Elements");
System.out.print("\t" + a1 + "\n");
 
List<String> l1 = new LinkedList<String>();
l1.add("Program");
l1.add("Creek");
l1.add("Java");
l1.add("Java");
System.out.println("LinkedList Elements");
System.out.print("\t" + l1 + "\n");
 
Set<String> s1 = new HashSet<String>(); // or new TreeSet() will order the elements;
s1.add("Program");
s1.add("Creek");
s1.add("Java");
s1.add("Java");
s1.add("tutorial");
System.out.println("Set Elements");
System.out.print("\t" + s1 + "\n");
 
Map<String, String> m1 = new HashMap<String, String>(); // or new TreeMap() will order based on keys
m1.put("Windows", "2000");
m1.put("Windows", "XP");
m1.put("Language", "Java");
m1.put("Website", "programcreek.com");
System.out.println("Map Elements");
System.out.print("\t" + m1);
```

输出结果：

``` bash
ArrayList Elements
	[Program, Creek, Java, Java]
LinkedList Elements
	[Program, Creek, Java, Java]
Set Elements
	[tutorial, Creek, Program, Java]
Map Elements
	{Windows=XP, Website=programcreek.com, Language=Java}
```

`译文原文地址：`[http://www.programcreek.com/2009/02/the-interface-and-class-hierarchy-for-collections/](http://www.programcreek.com/2009/02/the-interface-and-class-hierarchy-for-collections/)