---
title: Guava优美代码-3-Preconditions
date: 2016-10-20 01:17:02
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- Preconditions
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- Preconditions
---
## 前置条件Preconditions
> 前置条件：让方法调用的前置条件判断更简单。

在日常开发中，我们经常会对方法的输入参数做一些数据格式上的验证，以便保证方法能够按照正常流程执行下去。对于可预知的一些数据上的错误，我们一定要做事前检测和判断，来避免程序流程出错，而不是完全通过错误处理来保证流程正确执行，毕竟错误处理是比较消耗资源的方式。在平常情况下我们对参数的判断都需要自己来逐个写方法判断，代码量不少并且复用性不高。

Guava在Preconditions类中提供了若干前置条件判断的实用方法，强烈建议在Eclipse或者是Intellij IDEA中静态导入这些方法。每个方法都有三个变种：

- 没有额外参数：抛出的异常中没有错误消息；
- 有一个Object对象作为额外参数：抛出的异常使用Object.toString() 作为错误消息；
- 有一个String对象作为额外参数，并且有一组任意数量的附加Object对象：这个变种处理异常消息的方式有点类似printf，但考虑GWT的兼容性和效率，只支持%s指示符。

## Preconditions使用语法
|方法声明（不包括额外参数）	|描述	|检查失败时抛出的异常|
|--------|:--------|:--------|
|checkArgument(boolean)	|检查boolean是否为true，用来检查传递给方法的参数。	|IllegalArgumentException|
|checkNotNull(T)	|检查value是否为null，该方法直接返回value，因此可以内嵌使用|checkNotNull。	|NullPointerException
|checkState(boolean)	|用来检查对象的某些状态。	|IllegalStateException|
|checkElementIndex(int index, int size)	|检查index作为索引值对某个列表、字符串或数组是否有效。index>=0 && index<size *	|IndexOutOfBoundsException|
|checkPositionIndex(int index, int size)	|检查index作为位置值对某个列表、字符串或数组是否有效。index>=0 && index<=size |*	IndexOutOfBoundsException|
|checkPositionIndexes(int start, int end, int size)	|检查[start, end]表示的位置范围对某个列表、字符串或数组是否有效*	|IndexOutOfBoundsException|

## 代码示例

``` java
public class PreConditionsTest {

    @Test
    public void testPreConditionCheckState() {
        Weather weather = Weather.SHINNY;
        Preconditions.checkState(weather.equals(Weather.SHINNY), "Weather is not the best for a sunbath!");
    }

    @Test
    public void testPreConditionCheckNotNull() {
        List<String> members = Lists.newArrayList();
        Preconditions.checkNotNull(members, "members can not be null!");
        Preconditions.checkArgument(members.size() == 0, "members can not be null!");
    }
}
```

`注意`在编码时，如果某个值有多重的前置条件，建议你把它们放到不同的行，这样有助于在调试时定位。关于前置条件更多的使用方式，读者朋友们可以根据接口文档去尝试不同的方法，活学活用！