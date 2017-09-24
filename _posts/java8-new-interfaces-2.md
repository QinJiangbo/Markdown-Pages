---
title: 细说Java8之新增API下
date: 2017-03-31 00:19:19
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/java8-thumbnail.png
tags:
	- Java8
categories:
	- 开发技术
	- Java
keywords:
	- Java8
---
## Streams流
这里不得不说一下Java8中新出来的一个神器`java.util.Stream`。那么`java.util.Stream`是什么呢？我们说`java.util.Stream`表示一个可以对其中的元素进行一项或多项操作的序列。具体是什么呢？我们先说说这些操作可以是什么？这些操作可以是直接取得结果的，也可以只作为某一个中间过程。比如说针对`list`或者是`set`，这些`Stream`操作可以每次只对其中一个或者两个元素进行操作，作为中间过程保存；也可以针对全部的元素进行相关的操作。`Stream`操作可以串行执行也可以并行执行。

### 串行执行流
我们先看看串行执行的流操作是什么？首先创建一个`数据源`：

``` java
// 类似Guava中快速构造一个集合
public <T> List<T> addElements(T... elements) {
    List<T> collections = new ArrayList<T>();
    for (T t: elements) {
        collections.add(t);
    }
    return collections;
}

// 构造数据源集合
List<String> strings = addElements("hello", "wuhan", "university");
```
因为Java8中集合类型都被扩展了，所以我们可以直接通过`strings.stream()`来获取这个顺序执行的流。

#### Filter
我们想只打印`srtrings`中包括字符`h`的字符串，所以，我们可以这样做：

``` java
strings.stream()
    .filter((o) -> o.contains("h"))
    .forEach(System.out::println);

// hello
// wuhan
```
#### Sorted
如果需要对它排序再输出，可以这么做：

``` java
strings.stream()
    .sorted()
    .forEach(System.out::println);

// hello
// university
// wuhan
```
不要以为`strings`这个列表本身的顺序发生了变化？其实并没有，`streams.sorted()`方法仅仅是针对这个列表新建了一个视图（View）从而来表示这个排序后的列表。

#### Match
这个实际上就是来自于Guava中的Matcher类，主要是起一个判断作用，就是判断这个集合整体或者部分有什么性质。举下面这个例子：

``` java
boolean flag = strings.stream()
        .anyMatch((s) -> s.contains("v")); // true
System.out.println(flag);

flag = strings.stream()
        .allMatch((s) -> s.length() > 4); // true
System.out.println(flag);

flag = strings.stream()
        .noneMatch((s) -> s.contentEquals("world")); // true
System.out.println(flag);
```
这里需要给大家简单解释一下，`anyMatch`方法表示集合中的任意元素只要有一个满足该条件就行；而`allMatch`表示集合中的全部的元素全都得满足这个条件；`noneMatch`表示集合中的全部元素必须都不满足这个条件才行。目前Java8提供了这三种判断，想了解更多的判断类型，请大家去看看我的Guava系列博客。

#### Count
我们需要统计一个集合中满足条件的元素个数的时候可以使用这个方法来完成对应的操作：

``` java
long count = strings.stream()
        .filter((s) -> s.contains("v"))
        .count(); // 1
System.out.println(count);
```
可以看到我们非常方便地统计出了集合中包含字符`v`的元素的个数。

#### Map
映射使我们前面谈到的针对中间过程的操作，我们来看看它到底能做啥吧？

``` java
strings.stream()
        .map((s) -> s.toUpperCase())
        .forEach(System.out::println);

// HELLO
// WUHAN
// UNIVERSITY
```
我们可以看到`map`是针对每一个元素进行相关的操作，上面我们以**字符串的大写操作**将列表中的元素全部变成了大写状态。大家也可以根据自己的情况编写对应的方法。

#### Reduce
看完`Map`我们来聊聊`Reduce`操作，在Java8中，`Reduce`操作表示对集合中的连续两个元素进行操作，然后迭代，直到完成对所有元素的操作。最典型的例子就是进行集合中元素的求和：

``` java
List<Integer> ints = addElements(1, 4, 54, 34, 23, 12, 3);
int sum = ints.stream()
        .reduce((a, b) -> a + b)
        .get();
System.out.println(sum); // 131
```

### 并行执行流
说完了串行执行流，我们来说一说并行执行流是一种什么样的情况？我们知道，现在的电脑配置越来越高，CPU核心数也越来越多，那么如何有效地利用CPU的资源呢？这是一个很重要的方面，那就是采用并行的方式来进行编程。当然了，并行执行是否一定比串行执行快呢？这个不一定，大伙儿一定要根据自己的实际问题采取实际的措施解决，这里只是给大家介绍并行执行流如何使用，以及有哪些特点。

为了方便和串行执行流比较，我们先创建一个大没有重复元素的集合。

``` java
List<String> uuids = new ArrayList<>();
for (int i = 0; i < 1000000; i++) {
    uuids.add(UUID.randomUUID().toString());
}
```
首先使用串行执行的方式对它们进行排序：

``` java
long start = System.currentTimeMillis();
uuids.stream().sorted().count(); // 这里不加count()算不出消耗时间，很奇怪
long end = System.currentTimeMillis();
System.out.println((end - start) + "ms"); // 841ms
```
再使用并行执行的方式对它们进行排序：

``` java
start = System.currentTimeMillis();
uuids.parallelStream().sorted().count();
end = System.currentTimeMillis();
System.out.println((end - start) + "ms"); // 502ms
```
我们可以看到，并行的方式大概比串行的方式快了40%左右，这个性能的提升还是比较明显的。关于其它的比如说`allMatch`，`anyMatch`等等这些方法和那个串行执行流很像，这里就不多说了。

### Map
我们之前说过`Map`是不支持流操作的，但是不代表它不支持函数式编程操作，我们这里举几个例子说明一下：

``` java
Map<Integer, Integer> map = new HashMap<>();
for (int i = 0; i < 5; i++) {
    map.putIfAbsent(i, i);
}
map.forEach((i, s) -> System.out.println(s));

map.compute(2, (i, s) -> s + 1);
System.out.println(map.get(2)); // 3
```
我们在`Map`中最常见的一个操作就是修改某个元素的`value`值，上面的例子中我们使用`map.compute()`方法就可以实现相关的功能。并且和这个方法相似的还有两个方法：`map.computeIfPresent()`以及`map.computeIfAbsent()`方法。方法的名字已经说明它的作用了，相信也不用我说了。

还有一个方法是合并的功能，这里也说一下，不过我平时不怎么用到这个方法，但是直觉上感觉这个很重要：

``` java
// map.get(2) ==> 3
map.merge(2, 3, (value, newValue) -> value + newValue);
System.out.println(map.get(2)); // 6

// map.get(6) ==> null
map.merge(6, 6, (value, newValue) -> value + newValue);
System.out.println(map.get(6)); // 6
```
上面的例子表明，如果`map`中如果没有某一项`entry`的话，调用`merge`方法就只会向其中添加一个新的元素，如果存在`entry`的话，`merge`方法就会在原有的基础上添加这个新的值。

### Date API
Date API也是Java8中的一个新的亮点，为什么我们需要使用一个新的Date API？不知道大家有没有和我一样的感受，在IDE敲击Date的时候很容易选错为`java.sql.Date`而不是`java.util.Date`对吧？这两个不同的包下面为什么要搞出一样的名字？还有格式化时间的时候需要使用到`java.text.DateFormat`类。这都不算啥，我们如果想要简单的对日期进行加减，我的天，还需要转化来转化去才能成为我们需要的日期时间。后来我们不得不借助于`Joda Time`这第三方包的帮助，为啥会这样呢？因为这个API设计的时候我估计是给机器看的，而不是给人看的！所以，这次Java8中带来的改变我觉得很欣慰。下面我们来看一下这个时间日期API是怎么玩的？

#### LocaleDate

``` java
LocalDate localDate = LocalDate.now();
System.out.println(localDate.toString()); // 2017-03-31

localDate = LocalDate.of(2018, Month.JULY, 11);
System.out.println(localDate.toString()); // 2018-07-11

localDate = LocalDate.now(ZoneId.of("Asia/Shanghai"));
System.out.println(localDate.toString()); // 2017-03-31

localDate = LocalDate.ofEpochDay(365);
System.out.println(localDate.toString()); // 1971-01-01

localDate = LocalDate.of(2013, Month.JANUARY, 2).plusDays(1000);
System.out.println(localDate.toString()); // 2015-09-29

// how many days we've been together?
LocalDate from = LocalDate.of(2013, Month.JANUARY, 2);
LocalDate to = LocalDate.now();
Period days = Period.between(from, to);
System.out.println(days); // 29???

// another way
System.out.println(to.toEpochDay() - from.toEpochDay()); // 1549
```
我们可以利用Java8的LocalDate做很多日期相关的操作，比如`计算任意两个日期之间的天数差值`，`查看某个时区的日期`以及`查看多少天以后是一个不错的纪念日`等等这样的功能。

#### Instant
这个咋一看不知道它是干嘛的，其实嘛！就是时间戳！用于产生机器可读的时间。将日期时间以Unix格式保存。

``` java
Instant timestamp = Instant.now();
System.out.println(timestamp); // 2017-03-31T06:45:13.459Z
System.out.println(timestamp.getEpochSecond()); // 1490942821

// 用作Timestamp类构造参数
Timestamp.from(timestamp).getTime(); // 1490942821289
```

#### 格式化
说到时间日期，一定要提一提格式化，前一会吐槽了Java8以前的格式化，现在来看一看Java8是如何改进的。

``` java
LocalDateTime dateTime = LocalDateTime.parse("2017-01-09 22:23:30",
        DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss"));
System.out.println(dateTime); // 2017-01-09T22:23:30

dateTime = LocalDateTime.parse("Nov 24, 2017 22:10",
        DateTimeFormatter.ofPattern("MMM d, uuuu HH:mm"));
System.out.println(dateTime); // 2017-11-24T22:10
```
不得不说它这个`DateTimeFormatter`非常强大，什么格式都可以转，而且可以随意自定义格式，这个为我们程序开发提供了巨大的便利。

下面是一张表，大家可以对照这张表编写对应的格式`Pattern`：

|Symbol | Meaning|                     Presentation|      Examples|
| :------: | -------|                     ------------ |     -------|
|  G     |  era  |                       text             | AD; Anno Domini; A|
|   u    |   year     |                   year     |         2004; 04|
|  y    |   year-of-era       |          year       |       2004; 04|
|   D  |     day-of-year         |        number       |     189|
|  M/L  |   month-of-year     |          number/text   |    7; 07; Jul; July; J|
|   d  |     day-of-month      |          number      |      10|
|   Q/q  |   quarter-of-year     |        number/text   |    3; 03; Q3; 3rd quarter|
|   Y   |    week-based-year     |        year      |        1996; 96|
|   w   |    week-of-week-based-year  |   number     |       27|
|   W    |   week-of-month      |         number     |       4|
|   E   |    day-of-week       |          text       |       Tue; Tuesday; T|
|   e/c  |   localized day-of-week|       number/text  |     2; 02; Tue; Tuesday; T|
|   F   |    week-of-month             |  number            |3|
|   a  |     am-pm-of-day               | text              |PM|
|   h    |   clock-hour-of-am-pm (1-12)|  number |           12|
|   K    |   hour-of-am-pm (0-11)        |number     |       0|
|   k     |  clock-hour-of-am-pm (1-24)|  number  |          0|
|   H     |  hour-of-day (0-23)          |number          |  0|
|   m     | minute-of-hour              |number           | 30|
|   s       |second-of-minute          |  number        |    55|
|   S       |fraction-of-second         | fraction        |  978|
|   A       |milli-of-day                |number          |  1234|
|   n       |nano-of-second         |     number     |       987654321|
|   N       |nano-of-day               |  number        |    1234000000|

## 总结
这就是Java8中的Stream流，Map以及新增的Date API相关的内容啦。暂时先介绍这么多，后面我们再针对具体的内容慢慢聊。
