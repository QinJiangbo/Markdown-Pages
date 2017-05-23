---
title: Guava优美代码-12-Function
date: 2016-10-31 23:45:28
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- Function
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- Function
---
## Guava函数式编程
关于函数式编程，好像越来越多的编程语言都开始逐渐支持这个特性，最明显的就是python，javascript，还有Swift等等。Java终于在1.8中支持了函数式编程，但是在JDK1.8推出来以前，Guava就为我们提供了强大的函数式编程框架--Function。

## Function和Functions
`Function<A, B>`，它声明了单个方法`B apply(A input)`。Function对象通常被预期为引用透明的并且引用透明性中的”相等”语义与`equals`一致，如`a.equals(b)`意味着`function.apply(a).equals(function.apply(b))`。

`Functions`类为操作Function提供了很多方便的方法，比如合并两个函数`compose`，以及操作集合框架`forMap`等等。

## Function 使用案例
我们有一些`国家的首都`和`国家`的相关数据。如下：

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

现在对国家及国家的相关信息作如下处理：

### (1) 查找每个国家的首都信息

``` java
public List<String> transformCapitalsInUpperCase() {
        Function<Country, String> capitalCityFunction = new Function<Country, String>() {
            @Override
            public String apply(@Nullable CountryEnum country) {
                if (country == null) {
                    return null;
                }
                return country.getCapital().toUpperCase();
            }
        };

        List<String> capitalList = Lists.transform(countryList.findCountries(), capitalCityFunction);
        return capitalList;
    }
```

### (2) 查找每个国家的首都信息并将其转换为大写以及倒排

``` java
public List<String> composeTwoFunctions() {
        //改为大写
        Function<Country, String> upperCaseFunction = new Function<Country, String>() {
            @Override
            public String apply(@Nullable CountryEnum country) {
                if (country == null) {
                    return "";
                }
                return country.getCapital().toUpperCase();
            }
        };

        //倒排名称
        Function<String, String> reverseFunction = new Function<String, String>() {
            @Override
            public String apply(String s) {
                if (s == null) {
                    return null;
                }
                return new StringBuilder(s).reverse().toString();
            }
        };

        //混合方法
        Function<Country, String> composeFunction = Functions.compose(reverseFunction, upperCaseFunction);
        List<String> capitals = Lists.transform(countryList.findCountries(), composeFunction);
        return capitals;
    }
```

### (3) 从Map中加载数据到指定的List中

``` java
public List<String> forMapFunction() {
        Map<String, String> map = Maps.newHashMap();
        map.put(CountryEnum.CHINA.getName(), CountryEnum.CHINA.getCapital());
        map.put(CountryEnum.US.getName(), CountryEnum.US.getCapital());
        map.put(CountryEnum.KOREA.getName(), CountryEnum.KOREA.getCapital());
        map.put(CountryEnum.GB.getName(), CountryEnum.GB.getCapital());

        Function<String, String> capitalNameFromCountryFunction = Functions.forMap(map);

        List<String> countries = Lists.newArrayList();
        countries.add(CountryEnum.CHINA.getName());
        countries.add(CountryEnum.US.getName());

        List<String> capitals = Lists.transform(countries, capitalNameFromCountryFunction);
        return capitals;
    }
```

### (4) forMap函数测试二

``` java
public List<String> forMapFunction2() {
        Map<String, String> map = Maps.newHashMap();
        map.put(CountryEnum.CHINA.getName(), CountryEnum.CHINA.getCapital());
        map.put(CountryEnum.US.getName(), CountryEnum.US.getCapital());
        map.put(CountryEnum.KOREA.getName(), CountryEnum.KOREA.getCapital());

        Function<String, String> capitalNameFromCountryName = Functions.forMap(map, "Unknown");

        List<String> countries = Lists.newArrayList();
        countries.add(CountryEnum.CHINA.getName());
        countries.add(CountryEnum.GB.getName());

        List<String> capitals = Lists.transform(countries, capitalNameFromCountryName);
        return capitals;
    }
```

## 总结
Function使用的门槛会有点高，因为使用起来可能不是那么容易习惯。不过时间长了就会感受到函数式编程带来的乐趣，但是同时也必须提醒你，不要为了使用函数式编程而去使用函数式编程。这一点必须切记！