---
title: Guava优美代码-2-Optional
date: 2016-10-19 23:47:48
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- Optional
	- Not null
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- Optional
	- Not null
---
## 尽量不要使用NULL
轻率地使用null可能会导致很多令人吃惊的问题。通过学习Google底层代码库，我们发现95%的集合类不接受null值作为元素。我们认为， 相比默默地接受null，使用快速失败操作拒绝null值对开发者更有帮助。

此外，Null的含糊语义让人很不舒服。Null很少可以明确地表示某种语义，例如，Map.get(key)返回Null时，可能表示map中的值是null，亦或map中没有key对应的值。Null可以表示失败、成功或几乎任何情况。使用Null以外的特定值，会让你的逻辑描述变得更清晰。

Null确实也有合适和正确的使用场景，如在性能和速度方面Null是廉价的，而且在对象数组中，出现Null也是无法避免的。但相对于底层库来说，在应用级别的代码中，Null往往是导致混乱，疑难问题和模糊语义的元凶，就如同我们举过的Map.get(key)的例子。最关键的是，Null本身没有定义它表达的意思。

鉴于这些原因，很多Guava工具类对Null值都采用快速失败操作，除非工具类本身提供了针对Null值的因变措施。此外，Guava还提供了很多工具类，让你更方便地用特定值替换Null值。

## NULL在Java中的使用
1. 常见使用场景:
	<br>有时候，我们定义一个引用类型变量，在刚开始的时候，无法给出一个确定的值，但是不指定值，程序可能会在try语句块中初始化值。这时候，我们下面使用变量的时候就会报错。这时候，可以先给变量指定一个null值，问题就解决了。

2. 容器类型与null:
     + List：允许重复元素，可以加入任意多个null。
     + Set：不允许重复元素，最多可以加入一个null。
     + Map：Map的key最多可以加入一个null，value字段没有限制。
     + 数组：基本类型数组，定义后，如果不给定初始值，则java运行时会自动给定值。引用类型数组，不给定初始值，则所有的元素值为null。

3. null的其他作用
     - 判断一个引用类型数据是否null。 用==来判断。
     - 释放内存，让一个非null的引用类型变量指向null。这样这个对象就不再被任何对象应用了。等待JVM垃圾回收机制去回收。

## Guava中的Optional
大多数情况下，开发人员使用null表明的是某种缺失情形：可能是已经有一个默认值，或没有值，或找不到值。例如，Map.get返回null就表示找不到给定键对应的值。

Guava用Optional<T>表示可能为null的T类型引用。一个Optional实例可能包含非null的引用（我们称之为引用存在），也可能什么也不包括（称之为引用缺失）。它从不说包含的是null值，而是用存在或缺失来表示。但Optional从不会包含null值引用。

## Optional使用语法
静态方法：

|方法|说明|
|:---------------------------|:-------------------------------|
|Optional.of(T)|创建指定引用的Optional实例，若引用为null则快速失败|
|Optional.absent()|创建引用缺失的Optional实例|
|Optional.fromNullable(T)|创建指定引用的Optional实例，若引用为null则表示缺失|

非静态方法：

|方法|说明|
|:---------------------------|:-------------------------------|
|boolean isPresent()|如果Optional包含非null的引用（引用存在），返回true|
|T get()|返回Optional所包含的引用，若引用缺失，则抛出java.lang.IllegalStateException|
|T or(T)|返回Optional所包含的引用，若引用缺失，返回指定的值|
|T orNull()|返回Optional所包含的引用，若引用缺失，返回null|
|Set<T> asSet()|返回Optional所包含引用的单例不可变集，如果引用存在，返回一个只有单一元素的集合，如果引用缺失，返回一个空集合。|

## 使用Optional的意义
使用Optional除了赋予null语义，增加了可读性，最大的优点在于它是一种傻瓜式的防护。Optional迫使你积极思考引用缺失的情况，因为你必须显式地从Optional获取引用。直接使用null很容易让人忘掉某些情形，尽管FindBugs可以帮助查找null相关的问题，但是我们还是认为它并不能准确地定位问题根源。

如同输入参数，方法的返回值也可能是null。和其他人一样，你绝对很可能会忘记别人写的方法method(a,b)会返回一个null，就好像当你实现method(a,b)时，也很可能忘记输入参数a可以为null。将方法的返回类型指定为Optional，也可以迫使调用者思考返回的引用缺失的情形。

## 代码用例

OptionalTest.java

``` java
public class OptionalTest {

    @Test
    public void testOptional() {
        Optional<String> optional = Optional.of("Hello World!");
        if (optional.isPresent()) {
            System.out.println(optional.get());
        }
    }

    @Test
    public void testOptional2() {
        Optional<Integer> optional1 = Optional.of(6);
        Optional<Integer> optional2 = Optional.absent();
        Optional<Integer> optional3 = Optional.fromNullable(null);
        Optional<Integer> optional4 = Optional.fromNullable(10);

        if (optional1.isPresent()) {
            System.out.println("Optional1 is " + optional1.get());
        }

        if (optional2.isPresent()) {
            System.out.println("Optional2 is " + optional2.get());
        }

        if (optional3.isPresent()) {
            System.out.println("Optional3 is " + optional3.get());
        }

        if (optional4.isPresent()) {
            System.out.println("Optional4 is " + optional4.get());
        }
    }

    @Test
    public void testOptional3() {
        Optional<String> name = getNameListFromDB(null);
        if (name.isPresent()) {
            System.out.println("name: " + name.get());
        }
        System.out.println("orNull: " + name.orNull());
        Optional<String> address = getNameListFromDB("Wuhan Bayi Road");
        Set<String> set = address.asSet();
        System.out.println("toString: " + set.toString());
        System.out.println("size: " + set.size());
    }

    /**
     * 获得这个Optional返回类型
     *
     * @param DB
     * @return
     */
    private Optional<String> getNameListFromDB(String DB) {
        return Optional.fromNullable(DB);
    }
}
```

testOptional2的结果：（使用IDE-Jetbrains IDEA）

``` bash
Optional1 is 6
Optional4 is 10

Process finished with exit code 0
```
可以很轻易的避免这个NULL的问题，大家以后再开发中要经常使用Optional来避免这样的问题。