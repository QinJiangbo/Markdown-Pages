---
title: （译）Java高效的计数器
date: 2017-01-15 23:14:41
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
你可能需要一个计数器来统计来自于数据库或者某个文件的一些事物（比如单词数量）。Java中使用HashMap可以很简单地实现一个计数器。本文比较了实现计数器的不同方式。最后再总结得出一个比较有效率的计数器。

## 1. 朴素的计数器
简单地来说，可以这么实现一个计数器：

``` java
String s = "one two three two three three";
String[] sArr = s.split(" ");
 
//naive approach	 
HashMap<String, Integer> counter = new HashMap<String, Integer>();
 
for (String a : sArr) {
	if (counter.containsKey(a)) {
		int oldValue = counter.get(a);
		counter.put(a, oldValue + 1);
	} else {
		counter.put(a, 1);
	}
}
```

在每一次循环中，你都必须得检查这个键（Key）是不是存在。如果是，就把它原本的值加上1，如果不是，就把它设置为1。这个方式很简单而且很直接，但不是最有效的方式。为什么说它不是最有效的呢？主要基于以下两个原因：

- `containsKey()`和`get()`这两个方法在一个键（Key）存在的时候会被调用两次。那意味着它会搜索这整个Map两次。
- 因为整型（`Integer`）是不可变的，每一次的循环都会创建一个新的对象来容纳老的值加上1的结果。

## 2. 更好的计数器
自然地我们想要一个可变的整型数值去避免创建大量的整型对象，一个可变的整型（Integer）类可以这么定义：

``` java
class MutableInteger {
 
	private int val;
 
	public MutableInteger(int val) {
		this.val = val;
	}
 
	public int get() {
		return val;
	}
 
	public void set(int val) {
		this.val = val;
	}
 
	//used to print value convinently
	public String toString(){
		return Integer.toString(val);
	}
}
```

然后这个计数器可以这么改进：

``` java
HashMap<String, MutableInteger> newCounter = new HashMap<String, MutableInteger>();	
 
for (String a : sArr) {
	if (newCounter.containsKey(a)) {
		MutableInteger oldValue = newCounter.get(a);
		oldValue.set(oldValue.get() + 1);
	} else {
		newCounter.put(a, new MutableInteger(1));
	}
}
```

这个看起来似乎更好，因为它不再需要创建大量的整型对象了。然而，在一次循环中，如果一个键（Key）存在的情况下它依旧需要搜索两次。

## 3. 高效的计数器
这个`HashMap.put(key, value)`方法返回这个键（Key）的当前值。这是很有用的，因为我们能使用原有的值的引用来更新这个值而不需要再次搜索一遍。

``` java
HashMap<String, MutableInteger> efficientCounter = new HashMap<String, MutableInteger>();
 
for (String a : sArr) {
	MutableInteger initValue = new MutableInteger(1);
	MutableInteger oldValue = efficientCounter.put(a, initValue);
 
	if(oldValue != null){
		initValue.set(oldValue.get() + 1);
	}
}
```

## 4. 性能上的差异
下面的代码是为了测试这三种不同方式在性能上的差异。这个性能测试进行了100万次。原始的结果如下：

``` java
Naive Approach :  222796000
Better Approach:  117283000
Efficient Approach:  96374000
```

差异是很明显的，`223 vs 117 vs 96`是它们的相对耗时比值。在“天真”计数器和更好的计数器之间存在着巨大的差异，这也提示我们创建对象的开销是巨大的！

``` java
String s = "one two three two three three";
String[] sArr = s.split(" ");
 
long startTime = 0;
long endTime = 0;
long duration = 0;
 
// naive approach
startTime = System.nanoTime();
HashMap<String, Integer> counter = new HashMap<String, Integer>();
 
for (int i = 0; i < 1000000; i++)
	for (String a : sArr) {
		if (counter.containsKey(a)) {
			int oldValue = counter.get(a);
			counter.put(a, oldValue + 1);
		} else {
			counter.put(a, 1);
		}
	}
 
endTime = System.nanoTime();
duration = endTime - startTime;
System.out.println("Naive Approach :  " + duration);
 
// better approach
startTime = System.nanoTime();
HashMap<String, MutableInteger> newCounter = new HashMap<String, MutableInteger>();
 
for (int i = 0; i < 1000000; i++)
	for (String a : sArr) {
		if (newCounter.containsKey(a)) {
			MutableInteger oldValue = newCounter.get(a);
			oldValue.set(oldValue.get() + 1);
		} else {
			newCounter.put(a, new MutableInteger(1));
		}
	}
 
endTime = System.nanoTime();
duration = endTime - startTime;
System.out.println("Better Approach:  " + duration);
 
// efficient approach
startTime = System.nanoTime();
 
HashMap<String, MutableInteger> efficientCounter = new HashMap<String, MutableInteger>();
 
for (int i = 0; i < 1000000; i++)
	for (String a : sArr) {
		MutableInteger initValue = new MutableInteger(1);
		MutableInteger oldValue = efficientCounter.put(a, initValue);
 
		if (oldValue != null) {
			initValue.set(oldValue.get() + 1);
		}
	}
 
endTime = System.nanoTime();
duration = endTime - startTime;
System.out.println("Efficient Approach:  " + duration);
```

所以，当你使用一个计数器的时候，你可能需要一个函数根据值来排序这个Map。


`译文原文地址：`[http://www.programcreek.com/2013/10/efficient-counter-in-java/](http://www.programcreek.com/2013/10/efficient-counter-in-java/)