---
title: Guava优美代码-7-ImmutCollections
date: 2016-10-22 21:33:49
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- 不可变集合
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- 不可变集合
---
## 不可变集合Immutable Collections
### 为什么要使用不可变集合
不可变对象有很多**优点**，包括：

- 当对象被不可信的库调用时，不可变形式是安全的；
- 不可变对象被多个线程调用时，不存在竞态条件问题
- 不可变集合不需要考虑变化，因此可以节省时间和空间。所有不可变的集合都比它们的可变形式有更好的内存利用率（比如分析和测试细节）；
- 不可变对象因为固定不变，所以可以作为常量来安全使用。

创建对象的不可变拷贝是一项很好的**防御性编程**技巧。Guava为所有JDK标准集合类型和Guava新集合类型都提供了简单易用的不可变版本。

JDK也提供了Collections.unmodifiableXXX方法把集合包装为不可变形式，但我们认为**不够好**：

- 笨重而且累赘：不能合适地用在所有想做防御性拷贝的场景；
- 不安全：要保证没人通过原集合的引用进行修改，返回的集合才是事实上不可变的；
- 低效：包装过的集合仍然保有可变集合的开销，比如并发修改的检查、散列表的额外空间，等等。

如果你没有修改某个集合的需求，或者希望某个集合保持不变时，把它防御性地拷贝到不可变集合是个很好的实践。

`重要提示` 所有Guava不可变集合的实现都不接受null值。我们对Google内部的代码库做过详细研究，发现只有5%的情况需要在集合中允许null元素，剩下的95%场景都是遇到null值就快速失败。如果你需要在不可变集合中使用null，请使用JDK中的Collections.unmodifiableXXX方法。更多细节建议请参考“使用和避免null”。

## 不可变集合具体用例
### 创建不可变集合Builder()

``` java
@Test
public void testImmutableMapBuilder() {
    ImmutableMap<String, Integer> numbersMap = new ImmutableMap.Builder<String, Integer>()
            .put("one", 1)
            .put("two", 2)
            .put("three", 3)
            .build();
    System.out.println(numbersMap.get("two")); // 2
}

@Test
public void testImmutableSetBuilder() {
    ImmutableSet<String> nameSet = new ImmutableSet.Builder<String>()
            .add("hello", "I", "am", "Richard")
            .build();
    System.out.println(nameSet.toString()); // [hello, I, am, Richard]
}

@Test
public void testImmutableListBuilder() {
    ImmutableList<String> nameList = new ImmutableList.Builder<String>()
            .add("Hello", "World", "Guava", "Google")
            .build();
    System.out.println(nameList.toString()); // [Hello, World, Guava, Google]
}
```

### 创建不可变集合Creator()

``` java
@Test
public void testImmutableMapCreator() {
    ImmutableMap<String, Integer> numbersMap = ImmutableMap.of("one", 1, "two", 2,
            "three", 3, "four", 4);
    System.out.println(numbersMap.get("one")); // 1
}

@Test
public void testImmutableSetCreator() {
    ImmutableSet<String> nameSet = ImmutableSet.of("Hello", "name", "Henry");
    System.out.println(nameSet.toString()); // [Hello, name, Henry]
}

@Test
public void testImmutableListCreator() {
    ImmutableList<String> nameList = ImmutableList.of("Hello", "World", "Guava", "Google");
    System.out.println(nameList); // [Hello, World, Guava, Google]
}
```

## 神奇的copyOf方法
为什么说copyOf方法很神奇呢？因为ImmutableXXX.copyOf方法会尝试在安全的时候避免做拷贝。
在以下几种情况下copyOf方法会智能地判断从而避免线性时间拷贝：

- 在常量时间内使用底层数据结构是可能的——例如，ImmutableSet.copyOf(ImmutableList)就不能在常量时间内完成。
- 不会造成内存泄露——例如，你有个很大的不可变集合ImmutableList<String>
hugeList， ImmutableList.copyOf(hugeList.subList(0, 10))就会显式地拷贝，以免不必要地持有hugeList的引用。
- 不改变语义——所以ImmutableSet.copyOf(myImmutableSortedSet)会显式地拷贝，因为和基于比较器的ImmutableSortedSet相比，ImmutableSet对hashCode()和equals有不同语义。

在可能的情况下避免线性拷贝，可以最大限度地减少防御性编程风格所带来的性能开销。

``` java
@Test
public void testImmutableCollectionCopyOf() {
    ArrayList<String> nameList = Lists.newArrayList("Hello", "I", "like", "you", "OK");
    ImmutableList<String> nameList2 = ImmutableList.copyOf(nameList);
    ImmutableList<String> nameList3 = ImmutableList.copyOf(nameList.subList(1, 4));
    System.out.println(nameList2); // [Hello, I, like, you, OK]
    System.out.println(nameList3); // [I, like, you]
}
```

## asList视图

所有不可变集合都有一个asList()方法提供ImmutableList视图（**View**），来帮助你用列表形式方便地读取集合元素。例如，你可以使用sortedSet.asList().get(k)从ImmutableSortedSet中读取第k个最小元素。

asList()返回的ImmutableList通常是开销稳定的视图实现，而不是简单地把元素拷贝进List。也就是说，asList返回的列表视图通常比一般的列表平均性能更好，比如，在底层集合支持的情况下，它总是使用高效的contains方法。

``` java
@Test
public void testImmutableCollectionAsList() {
    ImmutableSet<String> nameSet = ImmutableSet.of("Hello", "Guava", "ISS", "WHU");
    System.out.println(nameSet.asList().get(1)); // Guava
    System.out.println(nameSet.asList()); // [Hello, Guava, ISS, WHU]
}
```