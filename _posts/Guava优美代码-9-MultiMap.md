---
title: Guava优美代码-9-MultiMap
date: 2016-10-24 23:44:12
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- MultiMap
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- MultiMap
---
## 多值映射MultiMap
Map大概是我们在生产过程中使用最多的容器之一了。Map是一种典型的K-V（键-值）结构。即可以通过一个Key（键）查找到对应的V（值）。正是由于此，JDK规定了Map中不能有重复的键，因为重复会导致无法查询准确的值。但是我们也要注意到这么一个需求：经常地，我们会需要一个键对应多个值，如果用JDK表示，就是这种结构`Map<String, List<String>>`但是这种结构非常的麻烦而且相对来讲过于复杂。因此Guava为了解决这个问题为我们带来了多值映射`MultiMap`。

可以用两种方式思考Multimap的概念：”键-单个值映射”的集合：<br>
`a -> 1 a -> 2 a ->4 b -> 3 c -> 5`<br>
或者”键-值集合映射”的映射：<br>
`a -> [1, 2, 4] b -> 3 c -> 5`

一般来说，Multimap接口应该用第一种方式看待，但asMap()视图返回Map<K, Collection<V>>，让你可以按另一种方式看待Multimap。重要的是，不会有任何键映射到空集合：**一个键要么至少到一个值，要么根本就不在Multimap中**。

## MultiMap使用实例

### 创建MultiMap并进行简单操作

``` java
@Test
public void testHowMultiMapWorks() {
    Multimap<String, String> multimap = HashMultimap.create();
    multimap.put("China", "Beijing");
    multimap.put("China", "Tianjing");
    multimap.put("China", "Wuhan");

    multimap.put("US", "Washington DC");
    multimap.put("US", "LA");
    multimap.put("US", "Seattle");
    multimap.put("US", "Brooklyn");

    System.out.println("size: " + multimap.size()); // size: 7
    System.out.println("elements list: " + multimap.get("China")); // elements list: [Beijing, Tianjing, Wuhan]
    System.out.println("keys length: " + multimap.keys().size()); // keys length: 7
}
```

### 迭代输出整个MultiMap

``` java
@Test
public void testMultiMapAsMap() {
    Multimap<String, String> multimap = HashMultimap.create();
    multimap.put("US", "Washington DC");
    multimap.put("US", "LA");
    multimap.put("US", "Seattle");
    multimap.put("US", "Brooklyn");
    Map map = multimap.asMap();
    // map.put("US", "hello"); // java.lang.UnsupportedOperationException
    Set<Map.Entry> entrySet = map.entrySet();
    for (Map.Entry entry : entrySet) {
        System.out.println(entry.getKey() + ":" + entry.getValue());
    }
    // US:[Washington DC, Brooklyn, Seattle, LA]
}
```

### 返回所有MultiMap键值对

``` java
@Test
public void testMultiMapEntries() {
    Multimap<String, String> multimap = HashMultimap.create();
    multimap.put("US", "Washington DC");
    multimap.put("US", "LA");
    multimap.put("US", "Seattle");
    multimap.put("US", "Brooklyn");
    Collection collections = multimap.entries();
    System.out.println(collections.toString());
    // 返回的是Multimap所有的键值对
    // [US=Washington DC, US=Brooklyn, US=Seattle, US=LA]
}
```

### 获取某一个值

``` java
@Test
public void testMultiMapGet() {
    Multimap<String, String> multimap = HashMultimap.create();
    multimap.put("US", "Washington DC");
    multimap.put("US", "LA");
    multimap.put("US", "Seattle");
    multimap.put("US", "Brooklyn");
    Collection<String> collection = multimap.get("US");
    System.out.println(collection.toString());
    // [Washington DC, Brooklyn, Seattle, LA]
}
```

## MultiMap的各种实现

MultiMap提供了多种实现，只要你需要使用到类似于`Map<String, Collection<String>>`结构的数据，都可以使用MultiMap:

|实现	|键行为类似	|值行为类似|
|-------------|---------------|----------------|
|ArrayListMultimap	|HashMap	|ArrayList|
|HashMultimap	|HashMap	|HashSet|
|LinkedListMultimap	|LinkedHashMap |	LinkedList|
|LinkedHashMultimap	|LinkedHashMap|	LinkedHashMap|
|TreeMultimap	|TreeMap	|TreeSet|
|ImmutableListMultimap	|ImmutableMap|	ImmutableList|
|ImmutableSetMultimap	|ImmutableMap|	ImmutableSet|

## 总结
尽管`Multimap`的实现用到了`Map`，但`Multimap<K, V>`不是`Map<K, Collection<V>>`。因为两者有明显区别：

- `Multimap.get(key)`一定返回一个非null的集合。但这不表示`Multimap`使用了内存来关联这些键，相反，返回的集合只是个允许添加元素的视图。
- 如果你喜欢像`Map`那样当不存在键的时候要返回null，而不是`Multimap`那样返回空集合的话，可以用`asMap()`返回的视图来得到`Map<K, Collection<V>>`。（这种情况下，你得把返回的Collection<V>强转型为List或Set）。
- `Multimap.containsKey(key)`只有在这个键存在的时候才返回true。
- `Multimap.entries()`返回的是`Multimap`所有的键值对。但是如果需要`key-collection`的键值对，那就得用`asMap().entrySet()`。
- `Multimap.size()`返回的是`entries`的数量，而不是不重复键的数量。如果要得到不重复键的数目就得用`Multimap.keySet().size()`。
