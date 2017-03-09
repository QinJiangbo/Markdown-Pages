---
title: （译）Java集合框架十问
date: 2016-12-25 20:42:11
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
下面这些问题是Stackoverflow上面关于Java集合框架提问最多和讨论最多的问题。在你开始看这些问题之前，你最好先看看这些类层级图，以确保你知道它们的关系。

## 1. 什么时候最好使用LinkedList而不是ArrayList？
从某种意义上来说，ArrayList就是数组。它的元素能够直接通过下标访问。但是如果这个数组快占满了，一个新的大的数组需要被分配出来，然后将原来的元素全部拷贝到新的数组里面去，这需要$O(n)$时间。同样的，添加或者移除某个元素需要将数组中剩下的元素移动。这恐怕是Arraylist最大的一个劣势了。

LinkedList是一条双向链接表。因此，当需要访问链表中间的某个元素时，它不得不从链表起始位置开始查找。另外，添加或者删除链表中的某个元素会更快，因为它只局部改变链表。

总结一下，最坏情况复杂度的比较如下：

|                 | Arraylist | LinkedList|
|--------------|---------------|-------------|
|get(index)        |    $O(1)$   |   $O(n)$|
|add(E)            |    $O(n)$   |   $O(1)$|
|add(E, index)     |    $O(n)$   |   $O(n)$|
|remove(index)     |    $O(n)$   |   $O(n)$|
|Iterator.remove() |    $O(n)$   |   $O(1)$|
|Iterator.add(E)   |    $O(n)$   |   $O(1)$|

除了运行时间以外，内存占用也是一个需要考虑的问题，尤其是针对大链表。在LinkedList中，每一个结点都需要至少两个额外的指针来链接前一个和后一个结点；而在ArrayList中，只需要一个元素的数组即可。

## 2. 在迭代一个集合框架的时候删除元素
在迭代集合框架的时候删除其中的一个元素，**只有一种正确的做法**，就是使用`Iterator.remove()`方法，例如：

``` java
Iterator<Integer> itr = list.iterator();
while(itr.hasNext()) {
   // do something
   itr.remove();
}
```

最常见的错误就是:

``` java
for(Integer i: list) {
  list.remove(i);
}
```
知道会报什么错误吗？会报`ConcurrentModificationException`这样一个异常。这是因为在`foreach`循环语句中产生了一个迭代器，这个迭代器来遍历这个链表，而与此同时，这个链表又被另一个`Iterator.remove()`改变了。在Java中，一个线程在遍历一个链表的同时不允许另一个线程对其进行修改操作。

## 3. 怎样把链表转化为int数组？
最简单的方法莫过于使用`Apache Commons Lang`库。

``` java
int[] array = ArrayUtils.toPrimitive(list.toArray(new Integer[0]));
```

在JDK中，没有捷径。你不能使用`List.toArray()`，因为那样会把链表转换为int的包装类数组`Integer[]`。正确的方式如下：

``` java
int[] array = new int[list.size()];
for(int i=0; i < list.size(); i++) {
  array[i] = list.get(i);
}
```

## 4. 怎样把int数组转化为链表？
同上，最简单的方法还是使用`Apache Commons Lang`库。

``` java
List list = Arrays.asList(ArrayUtils.toObject(array));
```

在JDK中，也没有捷径可走。

``` java
int[] array = {1,2,3,4,5};
List<Integer> list = new ArrayList<Integer>();
for(int i: array) {
  list.add(i);
}
```

## 5. 筛选一个集合框架元素的最好方式是什么？
同样的，你也可以使用第三方的包，比如Guava或者Apache Commons Lang。这两个包都有`filter()`方法（Guava里面的Collections2和Apache里面的CollectionUtils）。这个`filter()`方法将会返回指定预言（Predicate）下的元素。

在JDK中，这些事情会变得更加困难。但是Java 8为我们带来了好消息，那就是Java里面添加了预言（Predicate）的支持。但是现在你可能得使用迭代器（Iterator）来遍历整个集合。

``` java
Iterator<Integer> itr = list.iterator();
while(itr.hasNext()) {
   int i = itr.next();
   if (i > 5) { // filter all ints bigger than 5
      itr.remove();
   }
}
```
当然，你也可以模仿Guava和Apache Commons Lang中的实现方式，引用一个新的接口Predicate，这也是大多数高级程序员的做法。

``` java
public interface Predicate<T> {
   boolean test(T o);
}
 
public static <T> void filter(Collection<T> collection, Predicate<T> predicate) {
    if ((collection != null) && (predicate != null)) {
       Iterator<T> itr = collection.iterator();
          while(itr.hasNext()) {
            T obj = itr.next();
            if (!predicate.test(obj)) {
               itr.remove();
            }
        }
    }
}
```
下面的代码就简单了。

``` java
filter(list, new Predicate<Integer>() {
    public boolean test(Integer i) { 
       return i <= 5; 
    }
});
```
其实这个方法还可以直接使用lambda表达式代替。具体是什么自己实践吧！哈哈！

## 6. 将链表（List）转为集合（Set）的最简单方式是什么？
有两种方式来实现这个过程。第一种方式就是把一个链表直接塞到一个集合里去。这个时候重复主要是根据`hashCode()`来判断啦。在大多数情况这么做是可行的，但是你需要指明它们比较的方式。所以，推荐使用第二种方式，就是可以自己指定这个比较方式。

第一种方式

``` java
Set<Integer> set = new HashSet<Integer>(list);
```
第二种方式

``` java
Set<Integer> set = new TreeSet<Integer>(aComparator);
set.addAll(list);
```

## 7. 怎样从ArrayList中移除重复元素？
这个问题和上面的一问很相似。但是如果你不关心ArrayList中的元素顺序的话，上面的方式是可以的。然后再将集合中的元素放回列表。

``` java
ArrayList list = ... // initial a list with duplicate elements
Set<Integer> set = new HashSet<Integer>(list);
list.clear();
list.addAll(set);
```
如果你关心顺序的话，你可以把ArrayList中的元素放入LinkedHashSet中，然后执行上述过程。

## 8. 集合排序
在Java中维护一个排好序的集合有很多种方式。都是基于自然排序的方式或者是指定一个排序器。对于自然排序，你也需要在被排序的元素中实现Comparable接口。

1. `Collections.sort()`能够排序一个列表。这种排序是稳定的，而且复杂度为$O(nlog(n))$。
2. PriorityQueue提供了一种排好序的队列。PriorityQueue和`Collections.sort()`不同的地方在于PriorityQueue无论什么时候都维护着一个有序队列，每次你只能获得队首元素。你不能像`PriorityQueue.get(4)`这样随机访问里面的元素。
3. 如果集合中没有重复的元素，可以考虑使用TreeSet，和PriorityQueue一样，它也是每时每刻维护着一个有序的集合。你能得到最大值和最小值，但是你无法随机访问里面的元素。

简单来说，`Collections.sort()`就只是排序列表一次。而PriorityQueue和TreeSet一直都在排序这个集合，但是代价是不能随机访问里面的元素。

## 9. Collections.emptyList() vs new instance
这个问题同样适用于`emptyMap()`和`emptySet()`。

两个方法都返回一个空的列表。但是`Collections.emptyList()`返回一个不可变的列表。这意味着你不能添加元素到这个空列表。准确地来说，每次调用这个`Collections.emptyList()`都会复用这个空列表。这里用到的是单例模式的思想。

## 10. Collections.copy
有两种方式可以复制一个列表到另一个列表。一种是利用ArrayList构造函数。

``` java
ArrayList<Integer> dstList = new ArrayList<Integer>(srcList);
```

另一种是利用这个`Collecitons.copy()`方法，注意我们在分配一个目标列表的空间时，至少得和源列表的大小相同。

``` java
ArrayList<Integer> dstList = new ArrayList<Integer>(srcList.size());
Collections.copy(dstList, srcList);
```
两种复制都是**浅拷贝**，但是这两种方式有什么不一样呢？

首先，`Collections.copy()`方法不会重新分配目标列表的空间大小，即使目标列表的空间很小不足以装下所有的源列表元素。相反，它会抛出`IndexOutOfBoundsException`异常。有人会问这样做到底有什么好处呢？一种可能的原因就是它保证了程序在运行这个方法的时候时间是线性的。

其次，`Collecitons.copy()`方法仅接收List列表作为源列表和目标列表的类型。

`译文原文地址：`[http://www.programcreek.com/2013/09/top-10-questions-for-java-collections/](http://www.programcreek.com/2013/09/top-10-questions-for-java-collections/)