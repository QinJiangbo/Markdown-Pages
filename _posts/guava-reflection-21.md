---
title: Guava优美代码-21-反射
date: 2016-11-07 23:31:51
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- 反射
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- 反射
---
## Guava中的反射工具类
我们很多时候需要在运行时获取相关类的信息，比如它的方法有哪些，它的父类是什么，它的字段有哪些等等。其实这些JDK已经为我们做得很好了，我个人还是比较喜欢欣赏JDK里面做的反射处理的。不过这里为什么要讲Guava里面的反射呢？因为Guava对于JDK做了大量的封装和优化，最简单的比如动态代理，以前我们JDK里面要写一大堆，现在用Guava就能很方便的实现了。下面分别介绍Guava中的反射特性。

## 反射类使用实例

### 动态代理Dynamic Proxy

公共类(Student.java)

``` java
class Student implements People {

    String name;
    int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    /**
     * 打印People信息
     */
    public void printPeople() {
        System.out.println("name: " + name + ", age: " + age);
    }

    /**
     * 两个数之和
     *
     * @param a
     * @param b
     * @return
     */
    @ABC
    public int add(int a, int b) throws NumberFormatException {
        return a + b;
    }
}
```

公共接口(People.java)

``` java
interface People {
    void printPeople();
}
```

#### JDK实现

``` java
/**
 * 测试JDK的动态代理功能
 */
@Test
public void testJDKProxy() {
    People student = new Student("Qin Jiangbo", 23);
    People people = (People) Proxy.newProxyInstance(
            People.class.getClassLoader(),
            new Class[]{People.class},
            getHandler(student));
    people.printPeople();
}

public InvocationHandler getHandler(Object proxiedObject) {
    return (proxy, method, args) -> {
        System.out.println("method name: " + method.getName());
        System.out.println("args: " + (args == null ? "null" : args));
        return method.invoke(proxiedObject, args);
    };
}
```

#### Guava实现

``` java
/**
 * 测试Guava的动态代理
 */
@Test
public void testGuavaProxy() {
    People student = new Student("Qin Jiangbo", 23);
    People people = Reflection.newProxy(People.class, getHandler(student));
    people.printPeople();
}

public InvocationHandler getHandler(Object proxiedObject) {
    return (proxy, method, args) -> {
        System.out.println("method name: " + method.getName());
        System.out.println("args: " + (args == null ? "null" : args));
        return method.invoke(proxiedObject, args);
    };
}
```

### TypeToken
TypeToken类使用这种变通的方法以最小的语法开销去支持泛型类型的操作。

``` java
@Test
public void testTypeToken() {
    ArrayList<String> stringList = Lists.newArrayList();
    ArrayList<Integer> intList = Lists.newArrayList();
    // 类型被擦除了
    System.out.println(stringList.getClass().isAssignableFrom(intList.getClass()));
    // 利用TypeToken解析原来的类型
    TypeToken<ArrayList<String>> typeToken = new TypeToken<ArrayList<String>>() {
    };
    TypeToken<?> genericType = typeToken.resolveType(ArrayList.class.getTypeParameters()[0]);
    System.out.println(genericType.getType()); // class java.lang.String
}
```

### Invokable
Guava的Invokable是对java.lang.reflect.Method和java.lang.reflect.Constructor的流式包装。它简化了常见的反射代码的使用。(看了一下底层代码实现，好吧，真相就是几乎都是调用JDK的实现完成的。。。。)

``` java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface ABC {
    String value() default "Hello";
}

@Test
public void testInvokable() throws NoSuchMethodException {
    Invokable invokable = Invokable.from(Student.class.getMethod("add", int.class, int.class));
    System.out.println(invokable.isPublic()); // true
    System.out.println(invokable.getDeclaringClass()); // class com.qinjiangbo.Student
    System.out.println(invokable.getParameters()); // [int arg0, int arg1]
    System.out.println(invokable.getOwnerType()); // com.qinjiangbo.Student
    System.out.println(invokable.getExceptionTypes()); // [java.lang.NumberFormatException]
    System.out.println(invokable.getReturnType()); // int
    System.out.println(invokable.getModifiers()); // 1
    System.out.println(invokable.getName()); // add
    System.out.println(invokable.isOverridable()); // true
    System.out.println(invokable.isVarArgs()); // false
    System.out.println(invokable.isPublic()); // true
    System.out.println(invokable.isAbstract()); // false
    System.out.println(invokable.isAccessible()); // false
    System.out.println(invokable.isAnnotationPresent(ABC.class)); // true
    System.out.println(invokable.isStatic()); // false
}
```

## 总结
从上面的代码我们可以看到，相当一部分代码都被很好地封装了起来，为我们提供了非常方便的实现反射的接口。具体的操作就不细说了，大家对照着上面的测试代码跑一遍就会慢慢体会了。