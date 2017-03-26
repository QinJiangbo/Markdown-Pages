---
title: 细说Java8之新增API上（一）
date: 2017-03-24 19:55:51
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/java8-thumbnail.png
tags:
	- Java8
categories:
	- 开发技术
	- Java
keywords:
	- Java8
---
![JDK8](https://obrxbqjbi.qnssl.com/blog/image/java8-thumbnail.png)

JDK8发布已经有三年的时间了，虽然每次都在用JDK8去开发应用程序，但是在工作中对JDK8的新特性真的是使用不多。不过，我相信JDK8的开发者们这么辛辛苦苦编写出来的新特性一定是有它非常好的一面的，只是我们目前还没有习惯这种编程方式，**不熟悉它并不代表它不优秀！**

## 接口中新增的default方法
以前我们会说接口只能声明抽象方法，而抽象类才能既声明抽象方法又能实现具体方法。但是从JDK8开始，我们再也没有这种说法啦！因为，JDK8中为我们带来了接口的default默认方法。也就是说我们可以给接口中声明的方法加上默认的实现啦~

``` java
interface Formula {
    double calculate(int a);

    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}
```
我们可以看到`Formula`类有两个方法，一个是未实现的`calculate`方法，另一个是具有默认实现的`sqrt`方法。实现`Formula`接口的具体类不必实现`sqrt`就可以直接使用这个方法。

``` java
Formula formula = new Formula() {
    @Override
    public double calculate(int a) {
        return sqrt(a * 100);
    }
};

formula.calculate(100);     // 100.0
formula.sqrt(16);           // 4.0
```
是不是很Nice?但是我估计大家一时半会儿也很难使用这个特性，凡事得有一个过程。

## Lambda表达式
Lambda表达式可能是JDK8与之前版本最大的一个区别了！Lambda表达式在Python3中也开始支持了，二者的使用方式几乎相同，说明Lambda表达式已经被越来越多的平台支持啦，也说明Lambda表达式被越来越多的人所知晓。好啦，我们先来看看没有Lambda表达式的时候我们怎么写代码？

``` java
List<String> names = Arrays.asList("Richard", "Jack", "Pony");

Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return b.compareTo(a);
    }
});
```
这种方式够啦！！！代码好多都是可以省略的，其实真正起作用的就是`b.compareTo(a)`不是吗？那我们直接用它！

``` java
List<String> names = Arrays.asList("Richard", "Jack", "Pony");

Collections.sort(names, (String a, String b) -> {
	return b.compareTo(a)
});
```
还可以更简洁~

``` java
Collections.sort(names, (String a, String b) -> (b.compareTo(a)));
```
为什么还要加上类型？也可以去掉嘛！

``` java
Collections.sort(names, (a, b) -> b.compareTo(a));
```
Java编译器自己会**推断**出`a`和`b`的类型，所以我们只需要写出它们需要做什么就行~现在继续看看Lambda表达式会发挥出什么样的威力？


## 函数式接口
Java8中引入了函数式编程，但是这里需要给大家说明的是Lambda表达式适用的场景，Lambda表达式的实质是什么？**Lambda表达式就是一个匿名方法， 每一个Lambda表达式必须匹配一个只包含一个方法的接口。**这样的方法就是函数式接口，这种接口在以前也叫SAM（Single Abstract Method）单抽象方法类型。

我们可以使用任意的接口来作为Lambda表达式，只要这个接口只有一个抽象方法就行。为了保证在生产中不出现一些没有声明但是却满足条件的接口，这种很有可能是误报，所以我们需要使用`@FunctionalInterface`注解。这样编译器就会知道这个注解了，并且在你尝试向其中添加额外的抽象方法时就会报错。来看一个例子：

``` java
@FunctionalInterface
interface Converter<F, T> {
    T convert(F from);
}

Converter<String, Integer> converter = (from) -> Integer.valueOf(from);
Integer converted = converter.convert("123");
System.out.println(converted);    // 123
```
可以看到，`converter`对象的创建是基于一个抽象方法的实现。`(from) -> Integer.valueOf(from)`这个方法是`T convert(F from)`的具体实现。如果这个时候你再添加一个抽象方法，这个Lambda表达式就没法写了，所以，函数式接口只能允许有一个抽象方法。注意一个事实就是如果你省略了这个`@FunctionalInterface`注解这个代码依然是正确的。原因我们前面讲过。

## 方法和构造函数引用
利用静态方法引用上面的代码还可以写成这个样子：

``` java
Converter<String, Integer> converter = Integer::valueOf;
Integer converted = converter.convert("123");
System.out.println(converted);    // 123
```
你可能会问这个`::`是什么？没错，我们在C++里面看到过，它是作用域限定符。那么在Java8里面它的作用是什么呢？我们可以看到的是它可以被用来引用类的静态方法。其实在C++里面也有这样的作用，我估计Java8学习了这一点的。那除了引用静态方法，普通的实例方法能不能引用呢？答案当然是可以的！

``` java
class Something {
    String startsWith(String s) {
        return String.valueOf(s.charAt(0)); // convert first char to string
    }
}

Something something = new Something();
Converter<String, String> converter = something::startsWith;
String converted = converter.convert("Java");
System.out.println(converted);    // "J"
```
我们注意到类`Something`的实例对象`something`可以通过`::`来调用它的`startsWith`方法。以上是对方法的引用，我们来看看`::`对构造函数是如何引用的？

先定义一个对象：

``` java
public class Student {
	String name;
	int age;
	
	public Student(){}
	
	public Student(String name, int age) {
		this.name = name;
		this.age = age;
	}
}
```
然后定义一个创建学生对象的地方：

``` java
public interface School {
	Student create(String name, int age);
}
```
现在就可以这么创建学生对象啦：

``` java
School school = Student::new;
Student stuent = school.create("Richard", 23);
```
其实如果你使用IDE的话，上面的`Student::new`会自动提示出来。我们通过使用`Student::new`来引用了`Student`类的构造函数，而且Java的编译器会自己去匹配对应的构造函数。

## Lambda访问限定域
### 访问局部变量
你可以在匿名对象中访问外部的变量，但是需要注意的是，这个被访问的变量默认是final的，因此你不可以在匿名对象中修改这个变量。在Lambda表达式中也是这样，Lambda表达式外部的变量默认是final的，如果你修改这个变量的值编译就没法通过。

``` java
interface Calculator {
    int add(int a);
}

int b = 0;
Calculator calculator = ((a) -> { return a+b; });
b = 0;
```
这里就会报一个错误：`Variables used in lambda expression should be final or effectively final`。如何去修改呢？将`b = 0;`去掉即可。它就默认`int b = 0;`声明的这个变量`b`为`final`的了。为什么这些变量要设置为`final`不可变，那是因为考虑到多线程环境下更改这个变量的值是不安全的。

### 访问实例属性和静态变量
使用Lambda表达式访问实例属性和静态变量的时候，和访问局部变量不同，你不需要将这些属性或者变量设置为final类型。下面这样写也是合法的。

``` java
class Scopes {
    static int outerStaticNum;
    int outerNum;

    void testScopes() {
        Converter<Integer, String> stringConverter1 = (from) -> {
            outerNum = 23;
            return String.valueOf(from + outerNum);
        };

        Converter<Integer, String> stringConverter2 = (from) -> {
            outerStaticNum = 72;
            return String.valueOf(from + outerStaticNum);
        };
    }
    
    public static void main(String[] args) {
    	Scopes scopes = new Scopes();
    	scopes.testScopes();
    	System.out.println(outerStaticNum + "-" + outerNum);
    }
}
```
这段代码执行完毕的时候，请问`outerNum`和`outerStaticNum`的值各是多少？`23`和`72`?当然不是，都是零。**因为我们不能改变在Lambda表达式之外定义的值。**（但是匿名函数可以哦！）

### 访问default方法
在Java8中，虽然我们新增了默认方法即default方法，但是我们确不能使用Lambda表达式访问它。像下面这种方式就是编译不通过的：

``` java
interface Calculator {
    int add(int a);

    default int remove(int a) {
        return a -= 1;
    }
}

Calculator calculator = (a) -> remove(a);
```
它会提示`Cannot resolve method remove(a)`，说明Lambda表达式无法访问默认方法。

## 内置的函数式接口
Java8中的函数式接口有很多，其中有相当多的一部分来自于Google的开源项目Guava，如果对Guava不熟悉的同学可以看一下我的[《Guava优美代码》](http://t.cn/RMNYOyC)系列博客。下面让我们看看Java8为我们提供了哪些函数式接口？

- Predicates
- Functions
- Suppliers
- Consumers
- Comparators
- Optionals

### Predicates
中文翻译是什么，`预言`对吧。它的作用就是用来判断一些条件成不成立，而这些条件都会提前在`Predicate`对象中定义好。下面给出一个简单的例子：

``` java
Predicate<String> predicate = s -> s.startsWith("f");
System.out.println(predicate.test("funny")); // true
System.out.println(predicate.negate().test("funny")); // false
```
另外还有一些方法，比如`and`和`or`，这两个方法是用来创建`复合预言`的。

``` java
Predicate<String> predicate0 = s -> s.contains("k");
Predicate<String> andPredicate = predicate.and(predicate0);
Predicate<String> orPredicate = predicate.or(predicate0);
System.out.println(andPredicate.test("funk")); // true
System.out.println(orPredicate.test("joke")); // true
```
也可以使用`::`来创建`Predicate`对象：

``` java
Predicate<Boolean> nonNull = Objects::nonNull;
System.out.println(nonNull.test(null)); // false
```
可以看到Java8中的Predicate使用还是很多样的。但是Guava中的Predicate使用方式更多。留一个悬念，自己到本博客Guava系列博客中寻找答案吧！

### Functions
这个是啥意思大家都明白，`函数`嘛！函数的功能是干什么用的，执行一些动作！所以我们这里有必要给大家再区（啰）分（嗦）一下，**函数Function是用来执行一些操作的所以它有可能有返回值，有可能没有返回值；而预言Predicate主要是用来判断一些条件成不成立的，所以它的返回值只能是Boolean类型。**下面来看一下Function具体怎么用？

``` java
Function<String, Integer> function = Integer::valueOf;
Function<Integer, Boolean> function0 = (s) -> s.intValue() > 1000;
Function <String, Boolean> composedFunc = function.andThen(function0);
System.out.println(composedFunc.apply("123")); // false
```
是不是很好玩？很灵活吧！可以非常自由地组合一些方法。

### Suppliers
Suppliers又是啥？`提供者`？一般我们更倾向于将其称之为`生产者`。和`Function`不同的是`Supplier`不支持任何参数。

``` java
Supplier<Student> supplier = Student::new;
System.out.println(supplier.get()); // Student@133314b
```
啥也不说了，它就这一个方法。。。类似于一种工厂。

### Consumers
Consumers就是`消费者`，`消费者`的作用当然是消费从`生产者`那里产出的产品啦。我们来看看消费者到底如何开始消费？

``` java
Consumer<Student> consumer = p ->
        System.out.println(p.name + "+" + p.age);
consumer.accept(new Student("Richard", 23));
```
我们可以看到，现需要定义一个消费者，告诉它具体如何消费，然后通过`accept`方法给它传递一个产品供它消费。

### Comparators
比较器是JDK1.7就已经出现的函数式接口。JDK1.8中为这个函数式接口添加了需要默认方法。

``` java
Comparator<Student> comparator = (s1, s2) 
		-> s1.name.compareToIgnoreCase(s2.name);
Student student1 = new Student("Amy", 24);
Student student2 = new Student("Richard", 23);
System.out.println(comparator.compare(student1, student2)); // -17
```

### Optional
Optional就是`可选的`嘛！啥意思？也就是说返回的结构有可能是`Null`也有可能`Non-Null`。`Null`有合适和正确的使用场景，如在性能和速度方面`Null`是廉价的，而且在对象数组中，出现`Null`也是无法避免的。但相对于底层库来说，在应用级别的代码中，`Null`往往是导致混乱，疑难问题和模糊语义的元凶，就如同我们举过的`Map.get(key)`的例子。最关键的是，`Null`本身没有定义它表达的意思。

``` java
Optional<String> optional = Optional.of("hello");
System.out.println(optional.isPresent()); // true
System.out.println(optional.get()); // "hello"
System.out.println(optional.orElse("world")); // "hello"
optional.ifPresent((s) -> System.out.println(s.charAt(0))); // "h"
```
上面的例子是直接创建了一个`Optional`对象的，但是我们也可以在函数中返回一个`Optional`对象。

``` java
public Optional<String> getSomething(String thing){
    return Optional.ofNullable(thing);
}

if (getSomething("some").isPresent()) {
    System.out.println(getSomething("some").get());
}
if (getSomething(null).isPresent()) {
    System.out.println(getSomething(null).get());
} else {
    System.out.println("The value is null!");
}
```
一般我们都是先需要判断结果是否为空，然后在执行相应的操作。而且它比较好的一点是如果返回的值为空我们还可以指定相关的替代值。

## 总结
以上就是JDK1.8中给我们带来的新特性的一部分。关于Stream流以及Map, Reduce等部分的内容我们在下一篇博文里再探讨。