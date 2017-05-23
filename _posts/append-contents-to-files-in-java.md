---
title: （译）Java向文件中追加内容
date: 2017-02-25 20:01:13
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
## Replace vs Append/Add
如果你想要你的代码能够创建一个新的文件或者是清空之前已存在的一个文件内容，`FileWriter`能够简单地替代这些代码。为了替换一个文件中的所有内容，你可以这么做：

``` java
FileWriter fstream = new FileWriter(loc);
```
如果已经存在的文件名字和正在写入的文件名字重复了的话，上面的代码会删除已经存在的这个文件。

为了向一个已经存在的文件追加或者添加内容，仅需要像下面一样指定第二个参数为`true`即可。

``` java
FileWriter fstream = new FileWriter(loc, true);
```
这样的话，上述的代码就能够保证将数据追加至已经存在的文件当中而不是创建一个新的版本（的文件）。

## 完整的代码示例
下面是一段完整的代码示例，这段代码其实没什么特别的，只是为了大家快速查阅。

``` java
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
 
public class Main {
	public static void main(String[] args) throws IOException {
		File dir = new File(".");
		String loc = dir.getCanonicalPath() + File.separator + "Code.txt";
 
		FileWriter fstream = new FileWriter(loc, true);
		BufferedWriter out = new BufferedWriter(fstream);
 
		out.write("something");
		out.newLine();
 
		//close buffer writer
		out.close();
	}
}
```

`译者记`在Guava中其实还有更多类似于这样的方法，极大地提升了我们的编程效率！欢迎访问本站的[Guava教程](http://qinjiangbo.com/categories/%E5%BC%80%E5%8F%91%E6%8A%80%E6%9C%AF/Guava/)。

`译文原文地址：`[http://www.programcreek.com/2011/03/java-appendadd-something-to-an-existing-file/](http://www.programcreek.com/2011/03/java-appendadd-something-to-an-existing-file/)