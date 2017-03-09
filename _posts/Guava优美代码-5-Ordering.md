---
title: Guava优美代码-5-Ordering
date: 2016-10-20 10:02:42
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- Ordering
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- Ordering
---
## 优雅的Guava排序器
在开发中经常会对一些数据进行排序或者搜索操作，以前基本上都是先实现Comparator比较器，然后根据这个比较器去比较具体对象之间的顺序。这个过程一直都为开发者所诟病，现在Guava为我们带来了全新的一种比较的方式，那就是Ordering排序器！

排序器[Ordering]是Guava链式风格比较器[Comparator]的实现，它可以用来为构建复杂的比较器，以完成集合排序的功能。

从实现上说，Ordering实例就是一个特殊的Comparator实例。Ordering把很多基于Comparator的静态方法（如Collections.max）包装为自己的实例方法（非静态方法），并且提供了链式调用方法，来定制和增强现有的比较器。

## Ordering使用语法
### **创建方法**：常见的排序器可以由下面的静态方法创建

|方法	|描述|
|----------|--------|
|natural()	|对可排序类型做自然排序，如数字按大小，日期按先后排序|
|usingToString()	|按对象的字符串形式做字典排序[lexicographical ordering]|
|from(Comparator)	|把给定的Comparator转化为排序器|
|allEqual()|将所比较的所有对象视为一样的，没有顺序|

### **链式调用方法**：通过链式调用，可以由给定的排序器衍生出其它排序器

|方法	|描述|
|----------|--------|
|reverse()|	获取语义相反的排序器|
|nullsFirst()|	使用当前排序器，但额外把null值排到最前面。|
|nullsLast()|	使用当前排序器，但额外把null值排到最后面。|
|compound(Comparator)|	合成另一个比较器，以处理当前排序器中的相等情况。|
|lexicographical()|	基于处理类型T的排序器，返回该类型的可迭代对象Iterable<T>的排序器。|
|onResultOf(Function)|	对集合中元素调用Function，再按返回值用当前排序器排序。|

### **常规使用方法**：下面是一些常规的使用方法

|方法	|描述	|另请参见|
|----------|--------|-------|
|greatestOf(Iterable iterable, int k)|	获取可迭代对象中最大的k个元素。|	leastOf|
|isOrdered(Iterable)	|判断可迭代对象是否已按排序器排序：允许有排序值相等的元素。|	isStrictlyOrdered|
|sortedCopy(Iterable)	|判断可迭代对象是否已严格按排序器排序：不允许排序值相等的元素。|	immutableSortedCopy|
|min(E, E)	|返回两个参数中最小的那个。如果相等，则返回第一个参数。|	max(E, E)|
|min(E, E, E, E...)	|返回多个参数中最小的那个。如果有超过一个参数都最小，则返回第一个最小的参数。	|max(E, E, E, E...)|
|min(Iterable)	|返回迭代器中最小的元素。如果可迭代对象中没有元素，则抛出NoSuchElementException。	|max(Iterable), min(Iterator), max(Iterator)|

## Ordering具体用例

### Natural Ordering

``` java
    @Test
    public void testNaturalOrdering() {
        Ordering ordering = Ordering.natural(); //自然顺序排序
        int result1 = ordering.compare("cd", "ys");
        System.out.println(result1); // -22, 字符串首字母字典相对顺序
        int result2 = ordering.compare(11, 2);
        System.out.println(result2); // 1
//      int result3 = ordering.compare(1, "hello");
//      System.out.println(result3); // class cast exception
        int result4 = ordering.compare('d', 'a');
        System.out.println(result4); // 3, 字符在字典中的相对顺序
        int result5 = ordering.compare(1.5, 6.8);
        System.out.println(result5); // -1
    }
```

### ToString Ordering

``` java
    @Test
    public void testToStringOrdering() {
        Ordering ordering = Ordering.usingToString();
        int result = ordering.compare(578, "hello world");
        System.out.println(result);// 将所有比较元素转化为字符串再比较
    }
```

### Comparator Ordering

``` java
    @Test
    public void testFromComparatorOrdering() {
        Comparator<Worker> comparator = (o1, o2) -> o1.age - o2.age;
        Ordering ordering = Ordering.from(comparator);
        int result = ordering.compare(new Worker("Zhangsan", 23), new Worker("Lisi", 26));
        System.out.println(result); // -3,比较两人的年龄之差
    }
```

### Chain Invocation链式调用以及其他具体使用方法

``` java
@Test
public void testOrderingDetails() {
    Ordering ordering = Ordering.natural();
    // min
    System.out.println(ordering.min(1, 5)); // 1
    // min
    System.out.println(ordering.min(5, 4, 7, 8)); // 4
    // min
    Iterable iterable = Iterables.concat(Lists.newArrayList(31, 5, 8, 49, 24));
    System.out.println(ordering.min(iterable)); // 5

    // max
    System.out.println(ordering.max(iterable)); // 49

    // binary search
    System.out.println(ordering.binarySearch(
            Lists.newArrayList(14, 18, 19, 45, 76, 78), 45)); // 3, 表示第四个元素

    //加上链式调用
    ordering = ordering.nullsFirst().onResultOf(new Function<Worker, String>() {
        @Override
        public String apply(Worker input) {
            return input.name;
        }
    });
    List originList = Lists.newArrayList(
            new Worker("zl", 4), new Worker("sl", 34),
            new Worker("hp", 24), new Worker(null, 15),
            new Worker("app", 46), new Worker("lyh", 26));
    List sortedList = ordering.sortedCopy(originList);
    System.out.println(sortedList);
    // [Worker{name=null, age=15}, Worker{name=app, age=46}, ->
    // Worker{name=hp, age=24}, Worker{name=lyh, age=26}, ->
    // Worker{name=sl, age=34}, Worker{name=zl, age=4}]
}

class Worker {

    String name;
    int age;

    public Worker(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return MoreObjects.toStringHelper(this)
                .add("name", name)
                .add("age", age)
                .toString();
    }
}

```

## 总结
关于Ordering的使用方法还有很多，你可以根据自己的实际情况去应用其他的一些优美用法。本文列举出来的全部都是生产中比较常见的而且很重要的一些用法。
