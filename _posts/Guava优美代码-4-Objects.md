---
title: Guava优美代码-4-Objects
date: 2016-10-20 01:36:21
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- Objects
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- Objects
---
## 对象操作类Objects
Java语言中Object类是所有类的父类，其中有几个需要override的方法比如equals,hashCode和toString等方法。每次写这几个方法都要做很多重复性的判断, 很多类库提供了覆写这几个方法的工具类, Guava也提供了类似的方式。

关于equals方法需要多说几句，就是我们在写equals判断的时候很容易忘掉很多判断条件，往往容易导致结果不正确，以下的几条性质是在重写equals方法的时候必须遵守的：

1. **自反性reflexive**：任何非空引用x，x.equals(x)返回为true；
2. **对称性symmetric**：任何非空引用x和y，x.equals(y)返回true当且仅当y.equals(x)返回true；
3. **传递性transitive**：任何非空引用x和y，如果x.equals(y)返回true，并且y.equals(z)返回true，那么x.equals(z)返回true；
4. **一致性consistent**：两个非空引用x和y，x.equals(y)的多次调用应该保持一致的结果，（前提条件是在多次比较之间没有修改x和y用于比较的相关信息）；
5. 对于所有非null的值x， x.equals(null)都要返回false。 (如果你要用null.equals(x)也可以，会报NullPointerException)。

## Objects使用语法
|方法修饰符和类型|方法描述|
|------------|----------|
|static boolean	|**equal(Object a, Object b)** 判断两个对象是否完全一样。|
|static <T> T	|**firstNonNull(T first, T second) 已经过时**，请使用MoreObjects.firstNonNull(T, T)，这个方法计划在Guava21.0中移除。|
|static int	|**hashCode(Object... objects)** 批量的为一系列对象生成Hash值。|
|static Objects.ToStringHelper	|**toStringHelper(Class<?> clazz) 已经过时**，请使用 MoreObjects.toStringHelper(Class) 。这个方法计划在Guava21.0中移除。|
|static Objects.ToStringHelper	|**toStringHelper(Object self) 已经过时**，请使用 MoreObjects.toStringHelper(Object) 。这个方法计划在Guava21.0中移除。|
|static Objects.ToStringHelper	|**toStringHelper(String className) 已经过时**，请使用 MoreObjects.toStringHelper(String) 。这个方法计划在Guava21.0中移除。|

## Objects具体用例
下面是Objects源码片段中实现equals方法的片段：

``` java
  @CheckReturnValue
  public static boolean equal(@Nullable Object a, @Nullable Object b) {
    return a == b || (a != null && a.equals(b));
  }
```
说明Objects方法在调用的时候还是会使用方法体中的Object的equals方法。所以，用户在创建数据的同时，必须得重载equals。除非不需要比较两个模型。否则会得到一个错误的结果。

## 代码示例

### Objects.equal(a, b)

``` java
@Test
    public void testObjectsEquals() {
        Country country = new Country("CHINA", 2000, "BEIJING");
        Country country2 = new Country("CHINA", 2000, "BEIJING");
        System.out.println(country.equals(country2)); // true
    }

    @Test
    public void testObjectsEquals2() {
        System.out.println(Objects.equal("a", "b")); // false
    }

    @Test
    public void testObjectsEquals3() {
        Country country = new Country("CHINA", 2000, "BEIJING");
        Country country2 = new Country("CHINA", 2000, "BEIJING");
        System.out.println(Objects.equal(country, country2)); // true前提是对象要重写equals方法，一般这个Objects.equal方法主要用在重写的equal方法中
    }
```

Country.java

``` java
public class Country {

    private String name;
    private int time;
    private String capital;

    public Country() {

    }

    public Country(String name, int time, String capital) {
        this.name = name;
        this.time = time;
        this.capital = capital;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getTime() {
        return time;
    }

    public void setTime(int time) {
        this.time = time;
    }

    public String getCapital() {
        return capital;
    }

    public void setCapital(String capital) {
        this.capital = capital;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null) return false;
        if (!(o instanceof Country)) return false;
        Country country = (Country) o;
        return Objects.equal(name, country.getName())
                && Objects.equal(time, country.getTime())
                && Objects.equal(capital, country.getCapital());
    }

    @Override
    public int hashCode() {
        return Objects.hashCode(name, time, capital);
    }

    @Override
    public String toString() {
        return Objects.toStringHelper(this).add("name", name)
                .add("time", time)
                .add("capital", capital).toString();
    }

}
```

### Objects.hashCode(Object... objects)

``` java
    @Test
    public void testObjectsHash() {
        Country country = new Country("CHINA", 2000, "BEIJING");
        System.out.println(Objects.hashCode(country)); // 1955204803
    }
```

### MoreObject.toStringHelper(Object obj)

``` java
    @Override
    public String toString() {
        return Objects.toStringHelper(this).add("name", name)
                .add("time", time)
                .add("capital", capital).toString();
    }
    
    @Test
    public void testObjectsToString() {
        Country country = new Country("CHINA", 2000, "BEIJING");
        /* 这个方法目前在MoreObjects类中 */
        System.out.println(country.toString()); // Country{name=CHINA, time=2000, capital=BEIJING}
    }
```

### MoreObjects.firstNonNull(Object... objects)
这个方法主要是获取一系列值中第一个非空的值。

``` java
    @Test
    public void testObjectsFirstNonNull() {
        String name = null;
        String nickName = "Richard";
        System.out.println(MoreObjects.firstNonNull(nickName, name)); // Richard
    }
```

## 总结
Objects工具类在JDK类库的基础上扩展了许多实用的方法，这些都是在平时的工作生产中令我们产生痛点的地方，希望大家好好掌握Objects的用法。
