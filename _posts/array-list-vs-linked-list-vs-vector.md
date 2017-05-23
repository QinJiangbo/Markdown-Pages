---
title: （译）ArrayList vs. LinkedList vs. Vector
date: 2016-12-22 19:17:54
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/java8-2-thumbnail.png
tags:
	- JDK8
	- list
categories:
	- 开发技术
	- Java
keywords:
	- JDK8
	- list
---
## 1. List链表一览

链表，就像它名字说的一样，是一个由各个元素组成的有序序列。当我们谈论链表的时候，将它与集合Set相比较是一个不错的想法。集合Set由一组不重复的元素组成的无序集合。下面的一幅图描述了Java中集合框架的层次关系，通过这幅图你能对集合框架有一个不错的认识。

![](https://obrxbqjbi.qnssl.com/blog/image/arraylist.png)

## 2. ArrayList vs. LinkedList vs. Vector

从这个层次图来说，ArrayList，LinkedList以及Vector都实现了List接口。所以它们仨的使用非常相似。不同点主要体现在他们具体的实现不一样。使用的时候要根据它们的实现不同以及使用的场景不同选择合适的集合框架，以便于发挥它们最大的性能。

**ArrayList它的实现是基于一个可以改变大小的数组**。 因为允许更多的元素不断地加入到集合中去，所以ArrayList的大小是可以动态调整的。另外，它的元素可以直接通过下标访问，这是因为底层是数组。

**LinkedList它的实现基于双向链接表**。它的性能主要体现在**添加**和**移除**元素上面，在**访问元素**上面的性能比较差。

**Vector它和ArrayList非常接近，但是它是同步的**。如果你的代码是线程安全的，ArrayList将会是一个更好的选择。Vector和ArrayList都需要更多的空间来存放元素，因为会有更多的元素源源不断地加入进来。Vector每次都是扩大一倍它的容量size，而ArrayList每次都是扩大它的容量size的50%。LinkedList，因为实现的是Queue接口，所以它有比ArrayList和Vector更多的方法，比如`offer()`，`peek()`和`poll()`。

`注意`：**ArrayList默认的初始容量是非常小的。查看源码可以知道是10。这里希望大家养成一个好习惯，就是先估计一下自己的目标集合的容量，比如50，那么可以在申请ArrayList的时候就直接指定它的容量为50或者更高一点，避免每次重新调整大小而造成性能损失。**

## 3. ArrayList实例

``` java
ArrayList<Integer> al = new ArrayList<Integer>();
al.add(3);
al.add(2);           
al.add(1);
al.add(4);
al.add(5);
al.add(6);
al.add(6);

Iterator<Integer> iter1 = al.iterator();
while(iter1.hasNext()){
        System.out.println(iter1.next());
}
```

## 4. LinkedList实例

``` java
LinkedList<Integer> ll = new LinkedList<Integer>();
ll.add(3);
ll.add(2);           
ll.add(1);
ll.add(4);
ll.add(5);
ll.add(6);
ll.add(6);

Iterator<Integer> iter2 = ll.iterator();
while(iter2.hasNext()){
        System.out.println(iter2.next());
}
```

正如上面的实例所体现的，其实他们的用法都差不多，主要的不同在于**它们实现的机制不同而且它们执行相同操作的复杂度不同**。

## 5. Vector

**Vector几乎和ArrayList一样，它们俩之间的不同就是Vector是同步的**。因为这一点，它比ArrayList稍微耗性能一点。通常，除非你的代码明确指定要使用同步的代码，否则一般我不建议你使用Vector，使用ArrayList是一个比Vector更好的选择。

## 6. ArrayList vs. LinkedList性能对比

时间复杂度对比：

||ArrayList|LinkedList|
|---------------|:------------------:|:--------------:|
|get()		|$O(1)$			|$O(n)$|
|add()		|$O(1)$			|$O(1)$|
|remove()	|$O(n)$			|$O(n)$|

\* 表中`add()`指代`add(E e)`，`remove()`指代`remove(int index)`

- ArrayList在add/remove的随机索引访问上的复杂度为$O(n)$，而在链表的末尾进行的操作复杂度为$O(1)$。
- LinkedList在add/remove的随机索引访问上的复杂度为$O(n)$，在链表的首尾进行的操作复杂度为$O(1)$。

使用下面的代码来测试它们的性能：

``` java
ArrayList<Integer> arrayList = new ArrayList<Integer>();
LinkedList<Integer> linkedList = new LinkedList<Integer>();

// ArrayList add
long startTime = System.nanoTime();

for (int i = 0; i < 100000; i++) {
        arrayList.add(i);
}
long endTime = System.nanoTime();
long duration = endTime - startTime;
System.out.println("ArrayList add:  " + duration);

// LinkedList add
startTime = System.nanoTime();

for (int i = 0; i < 100000; i++) {
        linkedList.add(i);
}
endTime = System.nanoTime();
duration = endTime - startTime;
System.out.println("LinkedList add: " + duration);

// ArrayList get
startTime = System.nanoTime();

for (int i = 0; i < 10000; i++) {
        arrayList.get(i);
}
endTime = System.nanoTime();
duration = endTime - startTime;
System.out.println("ArrayList get:  " + duration);

// LinkedList get
startTime = System.nanoTime();

for (int i = 0; i < 10000; i++) {
        linkedList.get(i);
}
endTime = System.nanoTime();
duration = endTime - startTime;
System.out.println("LinkedList get: " + duration);



// ArrayList remove
startTime = System.nanoTime();

for (int i = 9999; i >=0; i--) {
        arrayList.remove(i);
}
endTime = System.nanoTime();
duration = endTime - startTime;
System.out.println("ArrayList remove:  " + duration);



// LinkedList remove
startTime = System.nanoTime();

for (int i = 9999; i >=0; i--) {
        linkedList.remove(i);
}
endTime = System.nanoTime();
duration = endTime - startTime;
System.out.println("LinkedList remove: " + duration);
```

结果是：

``` java
ArrayList add:  13265642
LinkedList add: 9550057
ArrayList get:  1543352
LinkedList get: 85085551
ArrayList remove:  199961301
LinkedList remove: 85768810
```

它们在性能上的区别很明显，就是**LinkedList在添加和移除的操作上更快，而在元素访问的操作上很慢**。基于复杂度表格以及这个实验的结果，我们能更好地使用这三个链表。事实上，如果存在下列情况，我建议你优先考虑LinkedList：

- 没有大量随机访问链表元素的需求；
- 存在大量的添加和移除操作；

`译文原文地址：`[http://www.programcreek.com/2013/03/arraylist-vs-linkedlist-vs-vector/](http://www.programcreek.com/2013/03/arraylist-vs-linkedlist-vs-vector/)