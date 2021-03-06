---
title: Java编程思想之数据存储（一）
date: 2017-03-14 22:09:44
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
## 数据到底存储在什么地方？
程序运行的时候，对象是怎么进行有效地放置的呢？尤其是我们关心的内存是如何分配的？一般来说，数据会存放在以下五个地点。

### 1. 寄存器
这是最快的存储区，没有之一。因为寄存器位于CPU内部。CPU是直接执行指令的地方，寄存器距离它最近，因此速度最快。但是寄存器的大小就比较可怜了，所以一般寄存器的大小都是根据需要进行分配。我们不能直接控制寄存器的内存分配，甚至根本感觉不到它的存在。

### 2. 堆栈
堆栈的概念不同于后面的**“堆”**。堆栈位于通用RAM（随机访问存储器）中，但是通过**堆栈指针**可以从处理器那里获得直接支持。若堆栈指针向下移动，则分配新的内存；若向上移动，则释放那些内存。这是一种快速有效的存储方法，仅次于寄存器。创建程序时，Java系统必须知道存储在堆栈中的所有项的确切生命周期，以便于上下移动堆栈指针。但是这个却限制了程序的灵活性。所以Java里面我们可以看到，虽然像**对象引用**这种数据存放在堆栈中，但是**对象**却存放在堆中。

### 3. 堆
也是一种通用的内存池，也位于RAM中。使用堆比堆栈的灵活性要高不少。编译器不需要知道堆中的对象需要存活多长的时间。需要一个对象的时候只需要使用一条简单的**new**语句就行了，当程序执行这段代码的时候，便会自动进行内存的分配。但是使用堆也会有一些弊端，就是灵活性是以牺牲了对应的对象创建和销毁的速度为代价的。

### 4. 常量存储
常量值直接放在程序代码内部。这样做是安全的，因为它们永远不会被改变。

### 5. 非RAM存储
这里说的非RAM存储其实指的就是数据的持久化问题。通过将数据持久化为文件的形式保存在磁盘上。它们不收任何程序的限制，只要你能够读取并识别出这些数据，无论是在哪台服务器，在哪个操作系统，Fine！Java的序列化就是一个典型的对象持久化的例子。它可以直接将对象以文件的形式保存下来，并且在需要的时候恢复。