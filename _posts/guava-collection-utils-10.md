---
title: Guava优美代码-10-CollectionUtils
date: 2016-10-29 22:08:52
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- Collection Utils
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- Collection Utils
---
## 集合工具类 Collection Utils Class
Guava的魅力在处理集合的方面可以说是展现的淋漓尽致，相比JDK的实现，使用Guava可以使我们的代码变得更加“优美”，尤其是在集合接口的声明或者是创建上面。下面我们通过一些简单的例子来领略一下Guava的魅力！

## 静态工厂方法
在JDK 7之前，构造新的泛型集合时要讨厌地重复声明泛型：

``` java
List<TypeThatsTooLongForItsOwnGood> list = new ArrayList<TypeThatsTooLongForItsOwnGood>();
```

我想我们都认为这很讨厌。因此Guava提供了能够推断泛型的静态工厂方法：

``` java
List<TypeThatsTooLongForItsOwnGood> list = Lists.newArrayList();
Map<KeyType, LongishValueType> map = Maps.newLinkedHashMap();
```

可以肯定的是，JDK7版本的钻石操作符(`<>`)没有这样的麻烦：

``` java
List<TypeThatsTooLongForItsOwnGood> list = new ArrayList<>();
```

但Guava的静态工厂方法远不止这么简单。使用工厂方法模式，我们可以方便地在初始化时就指定起始的元素。

``` java
Set<Type> copySet = Sets.newHashSet(elements);
List<String> theseElements = Lists.newArrayList("alpha", "beta", "gamma");
```

另外，通过为工厂方法命名，我们可以提高集合初始化大小的可读性：

``` java
List<Type> exactly100 = Lists.newArrayListWithCapacity(100);
List<Type> approx100 = Lists.newArrayListWithExpectedSize(100);
Set<Type> approx100Set = Sets.newHashSetWithExpectedSize(100);
```

## JDK vs Guava对照表

|集合接口	|属于JDK还是Guava	|对应的Guava工具类|
|--------------|:----------------------:|:---------------------:|
|Collection|	JDK	|Collections2|
|List	|JDK	|Lists|
|Set|	JDK	|Sets|
|SortedSet|	JDK|	Sets|
|Map|	JDK|	Maps|
|SortedMap|	JDK|	Maps|
|Queue|	JDK	|Queues|
|Multiset|	Guava|	Multisets|
|Multimap|	Guava|	Multimaps|
|BiMap|	Guava	|Maps|
|Table|	Guava	|Tables|

## Maps 使用案例
`Maps`类有很多非常有用的方法，比如我们前面提到的静态工厂方法，还有一些是直接对某个具体的`Map`集合对象的操作，都封装在了这个`Maps`类中，对一个`Map`集合的操作我们几乎可以全部依赖于这个`Maps`，当然，JDK提供也非常优秀！下面来看一下Guava中`Maps`类的具体使用方法：

``` java
@Test
public void testMaps() {
    // 创建Map -> 方法1
    ImmutableMap<String, Integer> left = ImmutableMap
            .of("Richard", 88, "Amy", 90, "Sarah", 87, "Lily", 89);
    ImmutableMap<String, Integer> right = ImmutableMap
            .of("Tom", 78, "Amy", 89, "Richard", 88);

    // 创建Map -> 方法2
    List<String> strings = Lists.newArrayList("Helloo", "World", "Beautiful", "good");
    ImmutableMap<Integer, String> stringsByIndex = Maps.uniqueIndex(strings,
            string -> string.length());
    System.out.println(stringsByIndex); // {6=Helloo, 5=World, 9=Beautiful, 4=good}

    MapDifference<String, Integer> diff = Maps.difference(left, right);

    // 取相同的Entry键值都相同
    System.out.println(diff.entriesInCommon()); // {Richard=88}

    // 取键相同值不同的元素
    System.out.println(diff.entriesDiffering()); // {Amy=(90, 89)}

    // 取左边有右边没有的元素
    System.out.println(diff.entriesOnlyOnLeft()); // {Sarah=87, Lily=89}

    // 取右边有左边没有的元素
    System.out.println(diff.entriesOnlyOnRight()); // {Tom=78}
}
```

我们可以看到`Maps`类对集合的创建，对一个或多个集合之间的比较等操作都有非常“高效，流畅”的方法！后面还有很多方法，大家可以自己多举几个例子去熟悉这个类的语法。

## Sets 使用案例
集合`Set`对我们来说使用的非常多，通常是在需要集合中所有的元素不能重复的情况下使用。为了让我们更好地使用`Set`类，Guava为我们又提供了`Sets`工具类，我们可以通过`Sets`工具类对任意一个Set集合进行相关的操作。

``` java
@Test
public void testSets() {
    Set<String> set1 = Sets.newHashSet("One", "Two", "Three", "Four");
    Set<String> set2 = Sets.newHashSet("Two", "Four", "Five", "Six");

    // 取交集
    Sets.SetView setView = Sets.intersection(set1, set2);
    System.out.println(setView.size()); // 2
    System.out.println(setView.toString()); // [Two, Four]

    // 取并集
    setView = Sets.union(set1, set2);
    System.out.println(setView.size()); // 6
    System.out.println(setView.toString()); // [Two, Three, One, Four, Five, Six]

    // 取差集
    setView = Sets.difference(set1, set2);
    System.out.println(setView.size()); // 2
    System.out.println(setView.toString()); // [Three, One]

    // 取笛卡尔积
    Set<List<String>> product = Sets.cartesianProduct(set1, set2);
    System.out.println(product); // [[Two, Five], [Two, Six], [Two, Two], [Two, Four], [Three, Five], [Three, Six], [Three, Two], [Three, Four], [One, Five], [One, Six], [One, Two], [One, Four], [Four, Five], [Four, Six], [Four, Two], [Four, Four]]

    // 集合所有子集
    Set<Set<String>> sets = Sets.powerSet(set1);
    System.out.println(sets); // powerSet({Two=0, Three=1, One=2, Four=3})
}
```
以上只是`Sets`集合的部分用法，是最常见最常用的一些用法，大家可以自己再去尝试其它的一些用法。

## Lists 使用案例
`List`可以说是我们在Java编程几乎最最拿手，最最“喜欢”的数据结构，因为它灵活，使用简单，尤其是`ArrayList`和`LinkedList`使用的最多，于是乎，Guava为我们锦上添花，提供了操作`List`集合的工具类`Lists`类。

``` java
@Test
public void testLists() {
    // 创造集合类-->直接newArrayList就行了
    List<String> names = Lists.newArrayList("Richard", "Amy", "Lily", "Sarah");
    List<Integer> numbers = Lists.newArrayList(11, 78, 89, 45, 30);
    System.out.println(names); // [Richard, Amy, Lily, Sarah]
    System.out.println(numbers); // [11, 78, 89, 45, 30]

    // 操作集合类-->反转
    List<String> reverseNames = Lists.reverse(names);
    List<Integer> reverseNumbers = Lists.reverse(numbers);
    System.out.println(reverseNames); // [Sarah, Lily, Amy, Richard]
    System.out.println(reverseNumbers); // [30, 45, 89, 78, 11]

    // 分割集合类
    List<List<String>> nameParts = Lists.partition(names, 2);
    List<List<Integer>> numberParts = Lists.partition(numbers, 2);
    System.out.println(nameParts); // [[Richard, Amy], [Lily, Sarah]]
    System.out.println(numberParts); // [[11, 78], [89, 45], [30]]
}
```
关于`Lists`集合的使用还有很多，这里列举了最常用的用法，结果通过注释给出来了。

## Iterables 使用案例
`Iterable`还有`Iterator`在刚学Java的时候一直是我傻傻分不清的两个东西，`Iterable`是啥？`Iterator`又是啥？蒙圈了！后来随着不断的系统训练，已经很明晰了，`Iterable`是`java.lang`包下的一个接口，而`Iterator`是`java.util`包下面的一个具体实现类。好了，我们都知道，JDK的集合框架类由于采用了`迭代器模式`，所以都实现`Iterable`接口，而Guava正好利用这个为我们提供了一个通用的集合工具类`Iterables`。通过这个类我们可以对任何类型的集合类进行一些相关的操作，非常方便！

``` java
@Test
public void testIterables() {
    List<Integer> ints1 = Lists.newArrayList(1, 2, 1, 4, 6, 9);
    List<Integer> ints2 = Lists.newArrayList(3, 7, 4, 6, 2, 1);

    // 融合两个列表
    Iterable<Integer> iterable = Iterables.concat(ints1, ints2);
    Iterator iterator = iterable.iterator();
    while (iterator.hasNext()) {
        System.out.print(iterator.next() + ",");
    } // 1,2,1,4,6,9,3,7,4,6,2,1,

    // 获取列表中第一个和最后一个元素
    int last = Iterables.getLast(ints1);
    System.out.println(last); // 9

    int first = Iterables.getFirst(ints1, 10000);
    System.out.println(first); // 1

    // 出现频率统计
    int count = Iterables.frequency(ints1, 1);
    System.out.println(count); // 2
}
```

## 总结
以上就是Guava中关于集合框架的一些操作工具类的使用情况，Guava的集合框架工具类现在正在进一步完善，我们可以持续保持关注，使用这些框架真的能改善我们代码的可读性和易用性。Guava完全符合《高效Java》中的软件工程的代码规范，对我来讲，Guava就是一件难得的艺术品，好好研究，大有裨益！