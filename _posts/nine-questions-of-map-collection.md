---
title: （译）Java Map集合九问
date: 2017-01-13 10:33:23
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
一般来说，Map就是一个包含一个或多个键值对（Key-Value Pair）的数据结构，而且每一个键（Key）不允许重复出现。本文总结了Java Map九个最常见的问题。为了通用性考虑，我在每一个例子中都使用了泛型。在例子中你可以认为K和V这两个参数都是默认实现了Comparable接口的。

## 1. 将Map转化为List ##
在Java中，Map接口提供了三种集合视图：key Set, value Set以及key-Value Set。通过List的构造函数或者是`addAll()`方法都可以将他们转化为List列表。下面的一段代码就是展示如何使用ArrayList的构造函数将Map转化为List。

``` java
// key list
List keyList = new ArrayList(map.keySet());
// value list
List valueList = new ArrayList(map.values());
// key-value list
List entryList = new ArrayList(map.entrySet());
```

## 2. 迭代一个Map ##
通过遍历每一个键值对（Key-Value Pair）来遍历一个Map是Map集合遍历中最基础的操作了。在Java中，这样的键值对（Key-Value Pair）都存储在一个叫`Map.Entry`的数据结构中。`Map.entrySet()`方法返回一个键值对集合（Key-Value Set），因此，最有效的遍历Map集合中每个Entry的方式就是：

``` java
for(Entry entry: map.entrySet()) {
  // get key
  K key = entry.getKey();
  // get value
  V value = entry.getValue();
}
```

也可以使用迭代器，尤其是在JDK1.5以前的版本中：

``` java
Iterator itr = map.entrySet().iterator();
while(itr.hasNext()) {
  Entry entry = itr.next();
  // get key
  K key = entry.getKey();
  // get value
  V value = entry.getValue();
}
```

## 3. 根据Key来排序一个Map ##
根据键（Keys）来排序一个Map是另一个比较基础的操作。一种可行的操作就是将`Map.Entry`放入一个列表（List），然后通过比较器（Comparator）来排序这个列表。

``` java
List list = new ArrayList(map.entrySet());
Collections.sort(list, new Comparator() {
 
  @Override
  public int compare(Entry e1, Entry e2) {
    return e1.getKey().compareTo(e2.getKey());
  }
 
});
```

`注意：`上述代码也可以使用Lambda表达式代替。

``` java
List list = new ArrayList(map.entrySet());
Collections.sort(list, (e1,e2)->e1.getKey().compareTo(e2.getKey()));
```
另一种方式就是使用`SortedMap`，它提供了一个基于键的排序机制。因此所有的键（Keys）必须实现`Comparable`接口或者是能被比较器（Comparator）接受。

`SortedMap`的另一种实现是`TreeMap`。它的构造函数能接受一个比较器（Comparator）。下面就展示了如何将一个普通的Map转化为一个排序的Map。

``` java
SortedMap sortedMap = new TreeMap(new Comparator() {
 
  @Override
  public int compare(K k1, K k2) {
    return k1.compareTo(k2);
  }
 
});
sortedMap.putAll(map);
```

## 4. 根据Value来排序一个Map ##
把Map放入一个List或者是排序同样也都适用于基于值（Value）这种情况。但是这一次需要比较的是`Entry.getValue()`。

``` java
List list = new ArrayList(map.entrySet());
Collections.sort(list, new Comparator() {
 
  @Override
  public int compare(Entry e1, Entry e2) {
    return e1.getValue().compareTo(e2.getValue());
  }
 
});
```
我们仍然可以使用一个SortedMap来解决这个问题，但是仅适用于所有的值都是唯一的情况。基于这样一个情况，你可以将键值对（Key-Value Pair）转化为值键对（Value-Key Pair）。这个解决方法有很大的限制性，所以不推荐大家使用。

## 5. 初始化一个不可变Map ##
当你需要使一个Map集合不可变时，一个最佳实践就是将这个Map放入一个不可变的Map中。这种防御性编程技术将会帮助你创建一个不仅使用安全而且也线程安全的Map集合。

为了初始化一个不可变Map集合，我们可以使用一个静态初始化块（如下）。这个方法的问题就是，尽管Map已经被声明为了`static final`，我们仍然可以在它初始化完毕后修改它的内容。比如`Test.map.put(3, "three");`。所以，这个并不是正真的不可变。通过静态初始化块去创建一个不可变的Map集合，我们需要额外的匿名类并且在初始化完毕的最后一步把它拷贝到一个不可修改的Map中。然后，当我们调用刚刚的那段代码时就会抛出`UnsupportedOperationException`的异常。

``` java
public class Test {
 
  private static final Map map;
  static {
    map = new HashMap();
    map.put(1, "one");
    map.put(2, "two");
  }
}
public class Test {
 
  private static final Map map;
  static {
    Map aMap = new HashMap();
    aMap.put(1, "one");
    aMap.put(2, "two");
    map = Collections.unmodifiableMap(aMap);
  }
}
```

Guava类库里面同样也支持不可变Map集合的多种初始化方式。更多关于Guava的教程请查看本博客的[Guava优美代码系列](http://t.cn/RMNYOyC)。

## 6. HashMap，TreeMap以及HashTable之间的区别 ##
在Java中，Map接口的实现类主要有三个：`HashMap`，`TreeMap`和`HashTable`。它们最主要的不同主要包括以下三点：

1. **迭代的顺序。HashMap**和**HashTable**不保证Map的迭代顺序。确切地说，它们不保证这个迭代的顺序一直保持为恒定。但是**TreeMap**将会根据键（Keys）或者比较器（Comparator）的自然排序来迭代整个Map。
2. **Key-Value键值对限制。HashMap**允许出现空键（null key）和空值（null values）。（空键只允许出现一次，因为我们知道，Map的键不能出现重复。）**HashTable**不允许出现空键（null key）和空值（null values）。如果**TreeMap**使用自然排序或者它的比较器（Comparator）不允许空值的话，添加空值或者空键将会抛出异常。
3. **同步（Synchronized）。**仅**HashTable**是同步的，其他的两个不是。所以说，如果一个线程安全的实现在项目中并不是那么需要，推荐大家使用**HashMap**而不是**HashTable**。

更完整的比较如下：

| | HashMap | Hashtable | TreeMap|
|----------------|--------|----------|---------------------|
|iteration order  | no      | no        | yes|
|null key-value   | yes-yes | no-no   | no-yes|
|synchronized     | no      | yes       | no|
|time performance | O(1)    | O(1)      | O(log n)|
|implementation   | buckets | buckets   | red-black tree|

## 7. 双向Map ##
有时候我们需要一组键值对（Key-Value Pair Set），意味着这个Map的值和键一样都是不能出现重复的。也就是一对一映射（One-to-One Map）。这个限制可以让我们创建出一个双向Map。所以我们也可以根据值（Value）来查找键（Key）。这样的数据结构被称为双向Map（bidirectional map），不幸的是JDK并不支持这个结构。

在Apache Commons和Guava中这个数据结构都有实现，分别叫做BidiMap和BiMap。但是都强制键和值之间的映射关系为1:1。

## 8. Map的浅拷贝 ##
在Java中Map的大多数实现，都提供了一种能拷贝其他Map的构造函数。但是这个拷贝的过程并**不是同步的（Synchronized）。**意味着一旦有某个线程拷贝了一个Map，另外的一个县城可能会对它进行修改。所以，为了避免不同步的拷贝，大家都应该使用`Collections.sysnchronizeMap()`。

``` java
Map copiedMap = Collections.synchronizedMap(map);
```
另一个有趣的浅拷贝就是使用`clone()`方法。**然而这个方法连Java集合框架的设计者Josh Bloch自己都不推荐使用。**在一次关于“构造函数拷贝VS克隆”的对话中他说道：

> 我经常在一个具体的类中提供公开的克隆方法`clone()`因为大家期待它。...但是很惭愧这样Cloneable就被打破了，但是它的确是发生了。...Cloneable是一个弱点，我希望大家看到它的局限性。

基于这个原因，我都不会告诉你如何利用`Clone()`方法去拷贝一个Map。

## 9. 创建一个空Map ##
如果一个Map是不可变得，使用

``` java
map = Collections.emptyMap();
```

否则，利用任何一种实现都OK，比如

``` java
map = new HashMap();
```

**完毕！**

`译文原文地址：`[http://www.programcreek.com/2013/09/top-9-questions-for-java-map/](http://www.programcreek.com/2013/09/top-9-questions-for-java-map/)