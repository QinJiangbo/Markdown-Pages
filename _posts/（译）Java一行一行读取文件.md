---
title: （译）Java一行一行读取文件
date: 2017-02-25 18:47:57
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
Java类里面关于输入输出（IO）这一块的类实在是太多了，以致于我们经常会感到迷惑到底使用哪一个类来完成我们的功能。下面的代码是介绍如何来使用Java IO类完成一行一行读取文件的需求。

**方法一：**

``` java
private static void readFile1(File fin) throws IOException {
	FileInputStream fis = new FileInputStream(fin);
 
	// 通过InputStreamReader来构造BufferedReader对象
	BufferedReader br = new BufferedReader(new InputStreamReader(fis));
 
	String line = null;
	while ((line = br.readLine()) != null) {
		System.out.println(line);
	}
 
	br.close();
}
```

**方法二：**

``` java
private static void readFile2(File fin) throws IOException {
	// 通过FileReader来构造BufferedReader对象
	BufferedReader br = new BufferedReader(new FileReader(fin));
 
	String line = null;
	while ((line = br.readLine()) != null) {
		System.out.println(line);
	}
 
	br.close();
}
```
使用下面的代码来测试：

``` java
//use . to get current directory
File dir = new File(".");
File fin = new File(dir.getCanonicalPath() + File.separator + "in.txt");
 
readFile1(fin);
readFile2(fin);
```
实践证明这两个代码都能很好地一行一行读取文本内容。

这两个方法的主要区别想必大家也能知道了，那就是构造一个`BufferedReader`对象时使用的参数不同。方法一使用的是`InputStreamReader`来构造的，而方法二使用的是`FileReader`来构造的。那么使用这两个不同的类具体有什么不同呢？

由Java文档可知，"An InputStreamReader is a bridge from byte streams to character streams: It reads bytes and decodes them into characters using a specified charset."意思就是`InputStreamReader`类是字节流到字符流的桥梁：它读取字节然后使用特定的字符集将它们转换为对应的字符。`InputStreamReader`类不仅仅只能够文件的输入流，它还可以处理其它的输入流，比如说网络连接，类路径资源，ZIP文件等等。

`FileReader`类是"Convenience class for reading character files. The constructors of this class assume that the default character encoding and the default byte-buffer size are appropriate." 意思是`FileReader`类是一个很便于读取字符文件的类。这个类的构造函数已经假定默认的字符编码方式和默认的字节缓冲数组大小是合理的。`FileReader`类允许你自己指定一个编码方式，除了使用平台默认的编码方式。因此，如果你的代码要在不同编码平台的系统上进行运行使用这个方式可能就不是个好主意。

总的来说，`InputStreamReader`总是比`FileReader`更安全的一个选择。

有必要在这里提醒大家一下，不管什么时候，在文件路径中都推荐大家使用`File.separator`的方式，而不是使用具体的`/`或`\\`。因为前一种方式不管在哪个平台，哪个操作系统，它都能保证这个路径的格式永远是正确的。另外，使用的文件路径最好也要写成相对路径。

### 更新
在Java1.7以后，你也可以使用下面的方式。重要的是，它和方法一其实是等效的。

``` java
Charset charset = Charset.forName("US-ASCII");
try (BufferedReader reader = Files.newBufferedReader(file, charset)) {
    String line = null;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
} catch (IOException x) {
    System.err.format("IOException: %s%n", x);
}
```

我们来具体看看`newBufferedReader(file, charset)`方法干了什么：

``` java
public static BufferedReader newBufferedReader(Path path, Charset cs){
 	CharsetDecoder decoder = cs.newDecoder();
 	Reader reader = new InputStreamReader(newInputStream(path), decoder);
 	return new BufferedReader(reader);
}
```
`译者记`和第一种方法一样吧？在Guava中其实还有更多类似于这样的方法，极大地提升了我们的编程效率！欢迎访问本站的[Guava教程](http://qinjiangbo.com/categories/%E5%BC%80%E5%8F%91%E6%8A%80%E6%9C%AF/Guava/)。

`译文原文地址：`[http://www.programcreek.com/2011/03/java-read-a-file-line-by-line-code-example/](http://www.programcreek.com/2011/03/java-read-a-file-line-by-line-code-example/)
