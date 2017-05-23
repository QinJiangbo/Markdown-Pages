---
title: Guava优美代码-20-Math
date: 2016-11-07 01:26:05
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- Math
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- Math
---
## Guava中的数学
数学处理有很多种方式，JDK里面也为我们提供相应的方法，为什么这里我们需要使用Guava里面方法呢？主要由以下几点原因：

- Guava Math针对各种不常见的溢出情况都有充分的测试；对溢出语义，Guava文档也有相应的说明；如果运算的溢出检查不能通过，将导致快速失败；
- Guava Math的性能经过了精心的设计和调优；虽然性能不可避免地依据具体硬件细节而有所差异，但Guava Math的速度通常可以与Apache Commons的MathUtils相比，在某些场景下甚至还有显著提升；
- Guava Math在设计上考虑了可读性和正确的编程习惯；`IntMath.log2(x, CEILING)`所表达的含义，即使在快速阅读时也是清晰明确的。而`32-Integer.numberOfLeadingZeros(x – 1)`对于阅读者来说则不够清晰。

## 数学工具类使用实例

``` java
package com.qinjiangbo;

import com.google.common.math.BigIntegerMath;
import com.google.common.math.DoubleMath;
import com.google.common.math.IntMath;
import org.junit.Test;

import java.math.BigInteger;
import java.math.RoundingMode;

/**
 * Date: 9/20/16
 * Author: qinjiangbo@github.io
 */
public class MathTest {

    @Test
    public void testSqrt() {
        System.out.println(IntMath.sqrt(26, RoundingMode.DOWN)); // 5
    }

    @Test
    public void testFactorial() {
        System.out.println(DoubleMath.factorial(10)); // 3628800.0
    }

    @Test
    public void testMean() {
        System.out.println(DoubleMath.mean(10, 276, 89, 46, 59)); // 96.0
    }

    @Test
    public void testDivide() {
        System.out.println(IntMath.divide(10, 3, RoundingMode.UP));
    }

    @Test
    public void testGcd() {
        System.out.println(IntMath.gcd(60, 24)); // 12
    }

    @Test
    public void testMod() {
        System.out.println(IntMath.mod(192, 54)); // 30
    }

    @Test
    public void testPow() {
        System.out.println(IntMath.pow(2, 10)); // 1024
    }

    @Test
    public void testIsPowerOfTwo() {
        System.out.println(IntMath.isPowerOfTwo(36)); // false
    }

    @Test
    public void testBinomial() {
        System.out.println(IntMath.binomial(10, 4)); // 210 [等于C(10, 4)]
    }

}

```

## 总结
上面的代码中方法名称是自解释型的，读者朋友可以自己查看这些代码来体会Guava是如何来处理数学中的常见运算的。