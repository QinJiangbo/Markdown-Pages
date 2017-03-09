---
title: Guava优美代码-17-IO操作
date: 2016-11-06 22:41:09
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/Guava-thumbnail.png
tags:
	- Guava
	- Files
categories:
	- 开发技术
	- Guava
keywords:
	- Guava
	- Files
---
## Guava文件IO操作
博主认为JDK中属文件IO的操作最麻烦而且最容易出错了，比如什么Stream啊，Reader啊，太多了，时间长了就容易记不住，经常写的话还是OK的，因为很熟练嘛，哈哈！那有没有什么方法能解放我们的文件操作呢？答案是Guava！Guava为我们提供了文件IO的各种工具类，其中使用最多的就是Files。下面我们仔细讨论Files的具体使用方式。

## Files使用实例

### (1) 读文件

``` java
public final String FILE_DIR = "/Users/Richard/Documents/R/";

@Test
public void testReadLines() throws IOException {
    String fileName = "data.txt";
    File file = new File(FILE_DIR + fileName);
    List<String> lines = Files.readLines(file, Charsets.UTF_8);
    for (String line : lines) {
        System.out.println(line);
    }
}

@Test
public void testReadFirstLine() throws IOException {
    String fileName = "data.txt";
    File file = new File(FILE_DIR + fileName);
    String line = Files.readFirstLine(file, Charsets.UTF_8);
    System.out.println(line); // I have a dream,
}

@Test
public void testFileToByteArray() throws IOException {
    String fileName = "data.txt";
    File file = new File(FILE_DIR + fileName);
    byte[] bytes = Files.toByteArray(file);
    for (byte bt : bytes) {
        System.out.println(bt + " : " + (char) bt);
    }
}

@Test
public void testFileToString() throws IOException {
    String fileName = "data.txt";
    File file = new File(FILE_DIR + fileName);
    String contents = Files.toString(file, Charsets.UTF_8);
    System.out.println(contents);
}

@Test
public void testNewReader() throws IOException {
    String fileName = "data.txt";
    File file = new File(FILE_DIR + fileName);
    BufferedReader bufferedReader = Files.newReader(file, Charsets.UTF_8);
    String line;
    while ((line = bufferedReader.readLine()) != null) {
        System.out.println(line);
    }
    bufferedReader.close();
}
```
上面的代码分别展示了如何从文件中读取数据，Guava的语法非常的简洁，极大地简化了我们读取文件的操作。而且提供了多种多样的方式来读取数据，比如`readLines`，`readFirstLine`，`fileToByteArray`以及`fileToString`等，开发者只需要关心文件中的数据，并不需要关心具体是怎么读取的。

### (2) 写文件

``` java
public final String FILE_DIR = "/Users/Richard/Documents/R/";

@Test
public void testWriteWithByteArray() throws IOException {
    String fileName = "data2.txt";
    File file = new File(FILE_DIR + fileName);
    String lyrics = "I will not make the same mistakes that you did\n" +
            "I will not let myself cause my heart so much misery\n" +
            "I will not break the way you did\n" +
            "You fell so hard\n" +
            "I learned the hard way, to never let it get that far";
    byte[] lyricsArray = lyrics.getBytes();
    Files.write(lyricsArray, file);
}

@Test
public void testWriteWithCharSequence() throws IOException {
    String fileName = "data2.txt";
    File file = new File(FILE_DIR + fileName);
    String lyrics = "I will not make the same mistakes that you did\n" +
            "I will not let myself cause my heart so much misery\n" +
            "I will not break the way you did\n" +
            "You fell so hard\n" +
            "I learned the hard way, to never let it get that far";
    CharSequence charSequence = lyrics.subSequence(0, lyrics.length() - 1);
    Files.write(charSequence, file, Charsets.UTF_8);
}

@Test
public void testNewWriter() throws IOException {
    String fileName = "data2.txt";
    File file = new File(FILE_DIR + fileName);
    String lyrics = "I will not make the same mistakes that you did\n" +
            "I will not let myself cause my heart so much misery\n" +
            "I will not break the way you did\n" +
            "You fell so hard\n" +
            "I learned the hard way, to never let it get that far";
    BufferedWriter bufferedWriter = Files.newWriter(file, Charsets.UTF_8);
    bufferedWriter.write(lyrics);
    bufferedWriter.close();
}
```
上面的代码主要是测试如何将数据写入文件，这里Guava提供了三种方式，以字节数组，以字符串以及新建一个`Writer`对象。博主推荐大家使用前面两种方式，因为大家无需手动地去关闭流，第三种需要开发者手动关闭这个`Writer`，稍不注意会引起一些错误。

### (3) 更新文件最后修改时间

```java
public final String FILE_DIR = "/Users/Richard/Documents/R/";

@Test
public void testTouch() throws IOException {
    String fileName = "data.txt";
    File file = new File(FILE_DIR + fileName);
    System.out.println(new Date(file.lastModified()));
    // Sun Nov 06 21:49:19 CST 2016
    Files.touch(file);
    System.out.println(new Date(file.lastModified()));
    // Sun Nov 06 22:14:26 CST 2016
}
```
这个我必须得单独拿出来介绍，因为博主发现这个方法名字取得太好啦，哈哈！和Linux中的`touch`命令一样，而且功能一样，都是更改文件的最后修改时间，如果文件不存在，就新建一个文件，真的是不得不佩服Guava的开发人员代码功底！代码可读性非常好！

## 总结
Guava中文件操作有很多，除了上面的读写修改之外，还有以下的操作，读者朋友们感兴趣的可以自己看一下：

|方法名|方法说明|
|-------|-------|
|createParentDirs(File)	|必要时为文件创建父目录|
|getFileExtension(String)	|返回给定路径所表示文件的扩展名|
|getNameWithoutExtension(String)	|返回去除了扩展名的文件名|
|simplifyPath(String)	|规范文件路径，并不总是与文件系统一致，请仔细测试|
|fileTreeTraverser()	|返回TreeTraverser用于遍历文件树|

文件操作值得花时间好好看一下！^_^！