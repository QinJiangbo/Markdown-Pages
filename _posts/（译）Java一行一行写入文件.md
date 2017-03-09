---
title: （译）Java一行一行写入文件
date: 2017-02-25 18:48:31
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/java-io-thumbnail.jpeg
tags:
	- Java
	- IO
categories:
	- 开发技术
	- Java
keywords:
	- Java
	- IO
---
这篇博客总结了能够用来将数据写入一个文件的一些类。

## 1. FileOutputStream

``` java
public static void writeFile1() throws IOException {
	File fout = new File("out.txt");
	FileOutputStream fos = new FileOutputStream(fout);
 
	BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(fos));
 
	for (int i = 0; i < 10; i++) {
		bw.write("something");
		bw.newLine();
	}
 
	bw.close();
}
```
这个例子使用了`FileOutputStream`类，相反你可以使用对文本文件操作更加友好的`FileWriter`类和`PrintWriter`类。

## 2. FileWriter

``` java
public static void writeFile2() throws IOException {
	FileWriter fw = new FileWriter("out.txt");
 
	for (int i = 0; i < 10; i++) {
		fw.write("something");
	}
 
	fw.close();
}
```

## 3. PrintWriter

``` java
public static void writeFile3() throws IOException {
	PrintWriter pw = new PrintWriter(new FileWriter("out.txt"));
 
	for (int i = 0; i < 10; i++) {
		pw.write("something");
	}
 
	pw.close();
}
```

## 4. OutputStreamWriter

``` java
public static void writeFile4() throws IOException {
	File fout = new File("out.txt");
	FileOutputStream fos = new FileOutputStream(fout);
 
	OutputStreamWriter osw = new OutputStreamWriter(fos);
 
	for (int i = 0; i < 10; i++) {
		osw.write("something");
	}
 
	osw.close();
}
```
## 5. 它们的不同
Java文档中说：

> `FileWriter`类是一个很适合写入字符文件的类。这个类的构造函数已经默认了文件的编码方式和字节缓冲数组的大小是可以接受的。如果想要手动指定这些值，你可以使用`FileOutputStream`来构造一个`OutputStreamWriter`类的对象。
> 
> `PrintWriter`类会将类按照既定的格式输出到文本文件当中。这个类实现了所有在`PrintStream`接口中的打印方法。它不包含写入原生字节的方法，因为一个程序需要使用未编码的字节流。

主要的区别就是`PrintWriter`类提供了一些额外的方法用来处理格式，比如`println`和`printf`。另外，`FileWriter`会在I/O失败时抛出`IOException`的异常。`PrintWriter`的方法不会抛`IOException`的异常，相反它会使用一个布尔型的信标（flag），而这个信标可以使用`checkError()`方法来标识错误。在每写入一字节的数据后`PrintWriter`都会自动地调用`flush`（清空）方法。在使用`FileWriter`类的时候，调用者需要小心调用`flush`方法。

`译者记`在Guava中其实还有更多类似于这样的方法，极大地提升了我们的编程效率！欢迎访问本站的[Guava教程](http://qinjiangbo.com/categories/%E5%BC%80%E5%8F%91%E6%8A%80%E6%9C%AF/Guava/)。

`译文原文地址：`[http://www.programcreek.com/2011/03/java-write-to-a-file-code-example/](http://www.programcreek.com/2011/03/java-write-to-a-file-code-example/)
