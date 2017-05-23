---
title: Java编程思想之this关键字（五）
date: 2017-04-06 21:41:27
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/ThinkingInJava.png
tags:
	- Java
	- 编程思想
categories:
	- 开发技术
	- Java
keywords:
	- Java
	- 编程思想
---
大家可能都知道`this`关键字，而且平时在工作中也用的非常多，那么你是真正知道`this`关键字的作用吗？如果一个类有两个实例**a**和**b**，那么你可能想知道如何让这两个实例都能调用`work()`方法呢？

``` java
class Person { void work(){ /** something **/ } }

public class PersonWorker {
	public static void main(String[] args) {
		Person a = new Person(),
			b = new Person();
		a.work();
		b.work();
	}
}
```
我想问的是`Person`类的`work()`方法是如何知道是哪个对象调用的呢？

### this
其实是这样的，在`Person`类编译好了以后，我们在`Person.class`的字节码文件中其实可以发现这个问题的答案，执行`javap -v Person`我们可以得到如下输出：

``` java
Classfile /Users/Richard/Downloads/Person.class
  Last modified Apr 6, 2017; size 232 bytes
  MD5 checksum 16d028d92ca1e1c36e9bf640a35c6f1a
  Compiled from "Person.java"
public class Person
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #3.#11         // java/lang/Object."<init>":()V
   #2 = Class              #12            // Person
   #3 = Class              #13            // java/lang/Object
   #4 = Utf8               <init>
   #5 = Utf8               ()V
   #6 = Utf8               Code
   #7 = Utf8               LineNumberTable
   #8 = Utf8               work
   #9 = Utf8               SourceFile
  #10 = Utf8               Person.java
  #11 = NameAndType        #4:#5          // "<init>":()V
  #12 = Utf8               Person
  #13 = Utf8               java/lang/Object
{
  public Person();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 1: 0

  void work();
    descriptor: ()V
    flags:
    Code:
      stack=0, locals=1, args_size=1 // 直接看到这一行
         0: return
      LineNumberTable:
        line 4: 0
}
SourceFile: "Person.java"
```
我们可以看到，在`wok()`方法里面多了一个局部变量`locals=1`，那么这个局部变量到底是啥呢？好吧，不卖关子了，就是我们当前实例对象的一个引用，不过我们在Java源码中并不能直接看到这个引用，那么我们如何获取它呢？`this`关键字！

this关键字还有很多很好玩的使用场景，比如说我们可以很优雅地实现类似于`StringBuffer`中的`append(s)`方法：

``` java
public class StringBaba {
	String s = "";
	public StringBaba append(String ss) {
		s = s + ss; // 这种实现有点不靠谱，这里仅供参考
		return this;
	}
	
	public static void main(String[] args) {
		StringBaba baba = new StringBaba();
		baba.append("hello").append("world").append("java");
	}
}
```

### 构造器中使用this
我们很多时候都不会再构造器中使用this关键字，但是万一要用到了呢？我们必须知道如何去处理这种情况。通常情况，我们使用`this`关键字都是表示**当前对象**或者**这个对象**，但是如果我们为`this`关键字添加了一些参数列表，那么这个时候就会有很明显的区别了。

``` java
public class Tower{
	int height;
	String name;
	
	Tower(int height) {
		this.height = height;
	}
	
	Tower(String name) {
		this.name = name;
	}
	
	Tower(String name, int height) {
		this(name);
		this.height = height;
	}
}
```
看到了我上面的用法了吗？这个时候`this(name)`一定指的是带字符串参数的那个构造函数，而且需要注意的是**this调用的构造函数必须置于最起始处，否则会报错，编译不通过。**

`注意`平时我们在构造函数里面赋值的时候我建议大家多使用`this.name=name`这种形式，因为`this.name`能将`name`区分开，而且语义也很清晰。

