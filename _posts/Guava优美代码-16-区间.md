---
title: Guava优美代码-16-区间
date: 2016-11-06 19:45:04
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- 区间
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- 区间
---
## Guava区间Range
区间，有时也称为范围，是特定域中的凸性（非正式说法为连续的或不中断的）部分。在形式上，凸性表示对`a<=b<=c`, `range.contains(a)`且`range.contains(c)`意味着`range.contains(b)`。

## Range的表示形式

|区间|数学表达式|
|:------|:---------|
|(a..b) |{a < x < b}|
|[a..b]| {a <= x <= b}|
|[a..b)| {a <= x < b}|
|(a..b]| {a < x <= b}|
|(a..+∞)| {x > a}|
|[a..+∞)| {x >= a}|
|(-∞..b)| {x < b}|
|(-∞..b] | {x <= b}|
|(-∞..+∞) |所有值|

上面的a、b称为端点 。为了提高一致性，Guava中的Range要求上端点不能小于下端点。上下端点有可能是相等的，但要求区间是闭区间或半开半闭区间（至少有一个端点是包含在区间中的）。

## 区间构建
|区间|方法|
|:------|:------|
|(a..b)	|open(C, C)|
|[a..b]	|closed(C, C)|
|[a..b)	|closedOpen(C, C)|
|(a..b]	|openClosed(C, C)|
|(a..+∞)	|greaterThan(C)|
|[a..+∞)	|atLeast(C)|
|(-∞..b)	|lessThan(C)|
|(-∞..b]	|atMost(C)|
|(-∞..+∞)	|all()|

## Range使用实例

``` java
package com.qinjiangbo;

import com.google.common.collect.*;
import org.junit.Test;

import java.util.ArrayList;
import java.util.Set;

/**
 * Date: 9/19/16
 * Author: qinjiangbo@github.io
 */
public class RangesTest {

    @Test
    public void testCheckThatElementIsInRange() {
        Range<Integer> range = Range.closed(2, 20); // [2, 20]
        Range<Integer> rangeWithRightOpen = Range.closedOpen(2, 20); // [2, 20)
        System.out.println(range.contains(20)); // true
        System.out.println(rangeWithRightOpen.contains(20)); // false
    }

    @Test
    public void testCheckThatRangeIsEnclosedInOtherRange() {
        Range<Long> range = Range.openClosed(2L, 20L); // (2, 20]
        Range<Long> smallerRange = Range.closed(1L, 15L); // [1, 15]
        System.out.println(range.encloses(smallerRange)); // false
        Range newRange = range.span(smallerRange);
        System.out.println(newRange); // [1..20]
        Range newRange2 = range.intersection(smallerRange);
        System.out.println(newRange2); // (2..15]
    }

    @Test
    public void testCheckThatAllElementIsInRange() {
        Range<Integer> range = Range.closed(2, 20);
        System.out.println(range.containsAll(Lists.newArrayList(10, 20, 18, 8, 5, 19)));
    } // true

    @Test
    public void testGenerateSetOfElementInRange() {
        Range<Integer> range = Range.open(2, 20);
        Range<Integer> integers = range.canonical(DiscreteDomain.integers());
        System.out.println(integers); // [3..20)
        System.out.println(integers.contains(6)); // true
        System.out.println(integers.contains(7)); // true
    }

    @Test
    public void testCreateRangeForGivenNumbers() {
        ArrayList<Integer> numbers = Lists.newArrayList(4, 3, 6, 8, 5, 9);
        Range<Integer> range = Range.encloseAll(numbers);
        System.out.println(range.lowerEndpoint() == 3); // true
        System.out.println(range.upperEndpoint() == 9); // true
    }

    @Test
    public void testRangeMap() {
        RangeMap<Integer, String> rangeMap = TreeRangeMap.create();
        rangeMap.put(Range.closed(1, 19), "foo");
        System.out.println("rangeMap: " + rangeMap); // rangeMap: [[1‥19]=foo]
        rangeMap.put(Range.open(3, 6), "bar");
        System.out.println("rangeMap: " + rangeMap); // rangeMap: [[1‥3]=foo, (3‥6)=bar, [6‥19]=foo]
        rangeMap.put(Range.open(10, 20), "foo");
        System.out.println("rangeMap: " + rangeMap); // rangeMap: [[1‥3]=foo, (3‥6)=bar, [6‥10]=foo, (10‥20)=foo]
        rangeMap.remove(Range.closed(5, 11));
        System.out.println("rangeMap: " + rangeMap); // rangeMap: [[1‥3]=foo, (3‥5)=bar, (11‥20)=foo]
    }

    @Test
    public void testRangeSet() {
        RangeSet<Integer> rangeSet = TreeRangeSet.create();
        rangeSet.add(Range.closed(1, 10));
        System.out.println("rangeSet: " + rangeSet); // rangeSet: [[1‥10]]
        rangeSet.add(Range.closedOpen(11, 15));
        System.out.println("rangeSet: " + rangeSet); // rangeSet: [[1‥10], [11‥15)]
        rangeSet.add(Range.open(15, 20));
        System.out.println("rangeSet: " + rangeSet); // rangeSet: [[1‥10], [11‥15), (15‥20)]
        rangeSet.add(Range.closedOpen(20, 25));
        System.out.println("rangeSet: " + rangeSet); // rangeSet: [[1‥10], [11‥15), (15‥25)]
    }
}

```
说明一下，上面的代码主要测试了区间的`contains`包含方法，`span`取并集方法，`intersection`取交集方法，`lowerEndpoint`和`upperEndpoint`取端点方法等方法。我们可以很方便的利用区间完成元素集合的创建和筛选。

## 总结
区间一般在开发中使用的不多，但是既然Guava为我们提供了如此好的方法，我们有必要好好了解和使用它。因为一旦我们的项目需要使用相关的操作时，Range区间能为我们提供最高效的解决方案。