---
title: Guava优美代码-8-MultiSet
date: 2016-10-24 12:42:01
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- MultiSet
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- MultiSet
---
## 多值集合MultiSet
使用过JDK的同学都知道，在Java中集合Set是不允许重复的，也就是说在同一个集合中不允许出现两个相同的元素。但是如果我们需要计算一个集合中一个元素出现的次数，采用JDK的方式去完成的话会非常的麻烦，但是Guava为我们解决了这个问题---MultiSet。

## 实现一个Word Count程序
我们需要统计一段文字中各个单词出现的次数，下面给出JDK和Guava两种实现方式。

### JDK实现

``` java
@Test
public void testJDK_WordCount() {
    String string = "I have a dream ! I dream one day I can go everywhere I wish !";
    String[] words = string.split(" ");
    Map<String, Integer> countMap = new HashMap<>();
    for (String word : words) {
        Integer count = countMap.get(word);
        if (count == null) {
            countMap.put(word, 1);
        } else {
            countMap.put(word, count + 1);
        }
    }
    Set<Map.Entry<String, Integer>> entrySet = countMap.entrySet();
    for (Map.Entry<String, Integer> entry : entrySet) {
        System.out.println(entry.getKey() + ":" + entry.getValue());
    }
}
```

### Guava实现

``` java
@Test
public void testGuava_WordCount() {
    String string = "I have a dream ! I dream one day I can go everywhere I wish !";
    String[] words = string.split(" ");
    List<String> wordList = Arrays.asList(words);
    Multiset<String> multiset = HashMultiset.create();
    multiset.addAll(wordList);
    for (String word : multiset.elementSet()) { // 返回不重复元素集合
        System.out.println(word + ":" + multiset.count(word));
    }
}
```

## MultiSet使用语法

|方法名称|方法描述|
|---------------|------------------|
|add(E element) |向其中添加单个元素|
|add(E element,int occurrences) |向其中添加指定个数的元素|
|count(Object element) |返回给定参数元素的个数|
|remove(E element) |移除一个元素，其count值 会响应减少|
|remove(E element,int occurrences)|移除相应个数的元素|
|elementSet() |将不同的元素放入一个Set中|
|entrySet()|类似与Map.entrySet 返回Set<Multiset.Entry>。包含的Entry支持使用getElement()和getCount()|
|setCount(E element ,int count)|设定某一个元素的重复次数|
|setCount(E element,int oldCount,int newCount)|将符合原有重复个数的元素修改为新的重复次数|
|retainAll(Collection c) |保留出现在给定集合参数的所有的元素|
|removeAll(Collectionc) |去除出现给给定集合参数的所有的元素|


## MultiSet使用示例

### 同一集合添加元素两次

``` java
@Test
public void testAddElementsTwoTimes() {
    Multiset<String> multiset = HashMultiset.create();
    multiset.add("one");
    multiset.add("one");
    System.out.println(multiset.count("one")); // 2
}
```

### 自定义删除添加修改元素数量

``` java
@Test
public void testUserCustomAddRemoveAndSetCount() {
    Multiset<String> multiset = HashMultiset.create();
    multiset.add("ball");
    multiset.add("ball", 10);
    System.out.println(multiset.count("ball")); // 11

    multiset.remove("ball", 5);
    System.out.println(multiset.count("ball")); // 6

    multiset.setCount("ball", 8);
    System.out.println(multiset.count("ball")); // 8
}
```

### 保留集合中被选中的元素（其余废弃）

``` java
@Test
public void testRetainOnlySelectedKeys() {
    Multiset<String> multiset = HashMultiset.create();
    multiset.add("ball");
    multiset.add("ball");
    multiset.add("cow");
    multiset.setCount("twelve", 12);

    multiset.retainAll(Arrays.asList("ball", "horse"));

    System.out.println("cow " + multiset.count("cow")); // cow 0
    System.out.println("ball " + multiset.count("ball")); // ball 2
}
```

## 总结
需要注意的是Multiset不是一个Map<E,Integer>,尽管Multiset提供一部分类似的功能实现。其它值得关注的差别有:

- Multiset中的元素的重复个数只会是正数，且最大不会超过Integer.MAX_VALUE。设定计数为0的元素将不会出现multiset中，也不会出现elementSet()和entrySet()的返回结果中。
- multiset.iterator() 会循环迭代每一个出现的元素，迭代的次数与multiset.size()相同。
- 如果想要知道每个元素的个数，需要使用multiSet.elementSet()方法去遍历整个集合。

Practice! Practice! Practice!
