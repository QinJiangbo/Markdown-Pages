---
title: Java编程思想之注释文档（二）
date: 2017-03-21 22:05:05
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
大家平时聊天的时候总会调侃到，程序员最讨厌的事情就是别的程序员代码不写注释，让自己更讨厌的是自己的代码居然要写注释！今天就聊一聊Java的代码注释以及文档。

## Java中的代码注释
Java中的代码注释主要有两种风格，一种是源自于C语言的传统注释风格，即我们平时见到的多行注释，以`/**`开头，以`**/`结尾。另一种源自于C++语言的单行注释风格，就是在每一行代码的后面都可以使用`//`来标识说明内容。

### 多行注释
``` java
/**
 * I want some coffee,
 * so I came here, pay you money
 * get a cup of coffee.
 */
```
`补充说明`上面这个多行注释如何在IDE中快速使用呢？直接输入`/**`注意是两个`*`，然后回车，啪！就出来了。

### 单行注释
``` java
// The weather is so nice in Wuhan!
```

## 注释文档
代码注释文档是保证Java源码可读性以及可维护性非常重要的一点，不然的话，别人写的代码你还能读？因此，很多时候一个新的员工到一个公司就会吵着要重构老员工的代码。为什么？原因很简单，第一，老员工的代码没加注释，代码风格比较烂，因此可读性很差；第二，这个新员工为了想展示自己的水平；这个时候，公司其他的老将通常会会心一笑，发出了too young~的叹息。

这里介绍一个工具---JAVADOC，即**Java Document**的简写。作为一款Java文档工具，它的作用是负责将Java的代码同注释文档链接起来，不然的话我们写代码的同时还要负责修改文档，太痛苦了。这个工具能帮助我们自动化地生成Java的注释文档，我们平时看到的HTML形式的JDK8文档指的就是这个。


需要说明的是，**所有的javadoc命令都只能在**`/**`**注释块中出现，在单行注释或者**`/*`**这种形式的多行注释都不会生效！**

有两种方式可以使用`javadoc`，一种是直接嵌入HTML页面，另一种是使用`独立文档标签`。这是个啥东西？就是一些以**“@”**字符开头的命令，且需要置于注释行的最前面。`行内文档标签`则可以出现在任何一个地方，也是以**“@”**字符开头的命令，不过他们需要使用花括号**"{}"**括起来。

### 嵌入式的HTML
嵌入式HTML的作用主要是为了对代码实现格式化。

例1

``` java
/**
 * <pre>
 * 	System.out.println("1+1=?")
 * </pre>
 */
```
例2

``` java
/**
 * These are the function names:
 * <ul>
 * 	<li>getUserNameById</li>
 * 	<li>login</li>
 * 	<li>register</li>
 * 	<li>logout</li>
 * </ul>
 */
```

### 文档标签
给大家介绍一些常用的文档标签。我们经常在Java源码，Spring框架，Struts框架中看到别的程序员写的代码注释，无不感叹多么详尽准确。现在就来看一看这些文档标签的真面目。

**@see**
@see标签允许用户引用其它类的文档。JAVADOC会在这个类生成的文档当中添加一个链接指向被引用的文档。具体格式如：`@see classname`和`@see classname#methodname`。

{**@link** package.class#member label}
@link标签必须写在花括号内，它和@see的作用差不多，只是它用在行内而@see用在行首而已。并且@link使用label作为链接文本而不是使用**"See Also"**，这个区别很关键。

{**@docRoot**}
@docRoot标签用于产生到文档根目录的相对路径，用于文档树页面的显示超链接。

{**@inheritDoc**}
@inheritDoc标签表示从当前这个类的**最直接的**基类中继承相关的文档作为当前的注释文档。

**@version**
@version标签表示代码版本号，一般使用这个以后我们就可以在javadoc中使用`javadoc -version`提取出相应版本的文档了。具体格式如：`@version version-info`。

**@author**
@author标签表示了这个代码的作者，这个很关键啊！一般多人协作时，需要在代码上加上作者信息以区分每个人的工作。具体格式如：`@author author-info`。

**@date**
@date标签表示创建这个文件的日期。具体格式`@date YYYY-MM-dd HH:mm:ss`或者`@date other-date-string`。

**@since**
@since标签表示这个代码适用的最早的JDK版本号，比如从JDK5起开始适用就可以表示为`@since 1.5`。

**@param**
@param标签其实是作用于方法体上面的标签，表示这个方法的参数信息。常见的用法`@param paramname description`。

**@return**
@return标签也是作用于方法体上面的标签，表示这个方法的返回信息，常见的用法`@return description`。

**@throws**
@throws标签也是作用于某个方法体上面的标签，表示这个方法可能抛出的异常类型，常见的用法`@throws full-qualified-class-name description`。中间的`full-qualified-class-name`表示这个异常类的全类名。

## 举个栗子
看了这么多标签，我们下面来实战一下：

``` java
/**
 * The class is to show the information in the specified file
 * @author qinjiangbo@github.io
 * @version 1.0.1
 * @since 1.7
 * @date 2017-03-22 12:41:20
 */
public class Tags {
	/**
	 * The method is going to print the file information
	 * @param fileName the name of the file
	 * @return the state of the operation
	 * @throws java.io.FileNotFoundException exception
	 */
	public int echo(String fileName) throws FileNotFoundException {
		return 0;
	}
}
```