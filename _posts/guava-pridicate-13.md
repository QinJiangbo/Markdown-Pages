---
title: Guava优美代码-13-Predicate
date: 2016-11-06 15:11:49
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- Predicate
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- Predicate
---
## Guava函数式编程
上一节篇文章我们说了Guava的`Function`函数的用法，这一节我们来说一说`Predicate`预言。`Predicate`的使用也是非常多的，因此读者朋友们也需要多多重视`Predicate`的用法。简单说一下`Predicate`和`Function`的区别，你可以理解为`Predicate`主要是用来**判断一些条件成不成立**的，而`Function`主要是用来**执行一些操作**的，侧重点不同。

## Predicate和Predicates
`Predicate<T>`，它声明了单个方法`boolean apply(T input)`。Predicate对象通常也被预期为无副作用函数，并且”相等”语义与equals一致。

同样我们说`Predicates`提供了更多构造和处理`Predicate`的方法，下面是一些例子：

|方法名|方法名|方法名|
|:--------:|:--------:|:------:|
|instanceOf(Class)	|assignableFrom(Class)|	contains(Pattern)|
|in(Collection)	|isNull()	|alwaysFalse()|
|alwaysTrue()	|equalTo(Object)|	compose(Predicate, Function)|
|and(Predicate...)	|or(Predicate...)|	not(Predicate)|

## Predicate使用案例
和前一节的`国家`-`首都`的例子一样，我们使用相同的数据来说明。这里我们还是列出他们的源码，方便大家阅读。

CountryEnum.java

``` java
package com.qinjiangbo.vo;

import com.google.common.collect.Lists;

import java.util.List;

/**
 * Date: 9/4/16
 * Author: qinjiangbo@github.io
 */
public enum CountryEnum {
    CHINA("CHINA", "BEIJING", 2000, "ASIA"),
    US("US", "WASHINGTON DC", 200, "AMERICA"),
    KOREA("KOREA", "SEUL", 500, "ASIA"),
    GB("GB", "LONDON", 1000, "EURO"),
    FINLAND("FINLAND", "", 1000, "EURO");

    private String name;
    private String capital;
    private int age;
    private String continent;

    private CountryEnum(String name, String capital, int age, String continent) {
        this.name = name;
        this.capital = capital;
        this.age = age;
        this.continent = continent;
    }

    public static List<CountryEnum> findCountries() {
        return Lists.newArrayList(CHINA, US, KOREA, FINLAND, GB);
    }

    public String getContinent() {
        return continent;
    }

    public void setContinent(String continent) {
        this.continent = continent;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getCapital() {
        return capital;
    }

    public void setCapital(String capital) {
        this.capital = capital;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

}

```

同样地，我们对`国家`-`首都`关系数据作出如下的判断：

### (1) 判断列表中的国家是否定义了首都

``` java
@Test
public void testHasCapitalDefined() {
    Predicate<CountryEnum> capitalCityProvidedPredicate = new Predicate<CountryEnum>() {
        @Override
        public boolean apply(@Nullable CountryEnum country) {
            return !Strings.isNullOrEmpty(country.getCapital());
        }
    };

    boolean allCountriesSpecifyCapital = Iterables.all(
            Lists.newArrayList(CountryEnum.CHINA, CountryEnum.GB, CountryEnum.FINLAND), capitalCityProvidedPredicate
    );

    System.out.println(allCountriesSpecifyCapital); // false
}
```
上面这个结果是`false`，因为我们可以看到，上面的枚举类中`FINLAND`的首都是没有定义的，为空，所以这个预言的结果是`false`。

### (2) 复合预言Composed Predicate

``` java
@Test
public void testComposedPredicate() {
    Predicate<CountryEnum> fromAsiaPredicate = new Predicate<CountryEnum>() {
        @Override
        public boolean apply(@Nullable CountryEnum country) {
            return country.getContinent().equals("ASIA");
        }
    };

    Predicate<CountryEnum> historyPredicate = new Predicate<CountryEnum>() {
        @Override
        public boolean apply(@Nullable CountryEnum country) {
            return country.getAge() > 1000;
        }
    };

    Predicate<CountryEnum> composedPredicate = Predicates.and(fromAsiaPredicate, historyPredicate);

    Iterable<CountryEnum> filteredCountries = Iterables.filter(CountryEnum.findCountries(), composedPredicate);

    Iterator iterator = filteredCountries.iterator();
    while (iterator.hasNext()) {
        System.out.println(iterator.next());
    }
    // CHINA
}
```
输出的结果是`CHINA`，因为从数据来看，只有中国既是亚洲国家，历史也超过了1000年。所以她是唯一合格的选项。

### (3) 预言包含关系

``` java
@Test
public void testContainsPredicate() {
    Predicate<CharSequence> containsPredicate = Predicates.containsPattern("\\d\\d");
    boolean isContained = containsPredicate.apply("hello world 21!");
    System.out.println(isContained); // true
}
```
这个例子和上面的`国家`-`首都`数据无关了，纯粹是一个功能点的说明，这里我们需要判断一个句子中是否包含我们需要检测的字符串。这里我们使用了正则表达式`\\d\\d`，表示匹配连续的两个数字，`hello world 21!`中有`21`，所以这段代码结果是`true`。

## 总结
简单总结一下，这篇博文里面知识简单地为大家介绍了`Predicate`类中的一些方法，其实`Predicate`类中还有很多实用的方法，大家可以自己调用Guava的相关方法好好体会。看的再多，不如自己动手敲一波！
