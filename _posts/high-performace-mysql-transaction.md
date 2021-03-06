---
title: 高性能MySQL之事务（三）
date: 2017-08-20 21:15:50
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/high-performance-mysql.png
tags:
	- 事务
	- MySQL
categories:
	- 数据科学
	- MySQL
keywords:
	- 事务
	- MySQL
---
## 事务
先说说什么是事务？不同的同学对这个概念有很多不同的理解。我以前对事务就有很长时间的不理解，单纯认为事务是一件很复杂的事情，到底多复杂，我也不清楚，总之就是需要处理各种操作的事情。现在给出一个比较准确的定义哈，**数据库中的事务就是指一组原子性的SQL查询，或者说一个独立的工作单元。**这句话怎么理解呢？你一共要执行一些SQL语句去完成某个操作对吧，但是完成的过程当中，执行到一半的时候，某个语句失败了，那么这个时候你的这些SQL语句就完不成这个操作了，因为数据有问题了，也就是说，在一个事务内的所有SQL语句，要么全部成功完成一个操作，要么就全部失败回滚。

### 银行应用示例
假设一个银行的数据库中存在两张表，分别是支票表（checking）和存储表（savings）。现在要从一个用户A想要从他的支票账户里面转移200元到存储账户，那么他需要经过三个步骤：

+ 检查支票账户月是否高于200元
+ 从支票账户里减除200元
+ 在存储账户里添加200元

这三个步骤的操作必须包括在同一个事务当中，任何一个步骤失败，则必须回滚所有的步骤。那么应该如何来做呢？

在MySQL中，我们可以使用`START TRANSACTION`语句来表示开启了一个事务，然后要么使用`COMMIT`提交事务将修改的数据持久化入库，要么就使用`ROLLBACK`撤销所有的修改。事务SQL的示例如下：

``` sql
START TRANSACTION;
SELECT balance FROM checking WHERE customer_id = 10249076;
UPDATE checking SET balance = balance - 200 WHERE customer_id = 10249076;
UPDATE savings SET balance = balance + 200 WHERE customer_id = 10249076;
COMMIT;
```

这些SQL啥子意思呢？也就是说，这几句SQL只要有一句比如`UPDATE savings SET balance = balance + 200 WHERE customer_id = 10249076;`执行失败了，那么整体就必须回滚！也就是说前面执行的`UPDATE checking SET balance = balance - 200 WHERE customer_id = 10249076;`就会被撤销，不再会写入库中去。

## ACID四原则
前面提到了事务的基本概念，说到事务，不说ACID等于耍流氓！所以，这里好好聊聊ACID四中类型。分别是原子性（Atomicity），一致性（Consistency），隔离性（Isolation）和持久性（Durability）。一个设计良好的系统，必须具备这几个特性。下面分别说说这个几个特性：

### 原子性Atomicity
**一个事务必须被视为一个不可分割的最小工作单元**，整个事务中所有的操作要么全部提交成功，要么全部失败回滚，对于一个事务来说，不可能只执行其中的一部分操作，这就是事务的原子性。

### 一致性Consistency
**数据库总是从一个一致性的状态转换到另一个一致性的状态**。在银行示例这个例子中，一致性确保了，当系统执行完第三、四条语句之间时崩溃，支票账户中也不会损失200元，因为事务还没有被提交，所以事务中的操作是不会写入到数据库中的。

### 隔离性Isolation
通常来说，**一个事务所做的修改在最终提交以前，对其他事务是不可见的。**也就是说，前面的第三条语句执行完，第四条语句还没有开始执行的时候，可能会有另一个汇款的程序开始执行，这个时候另一个程序看到的支票账号余额还是减掉200元以前的状态，前一个事务所做的操作对这个程序是不起作用的，也就是说“是不可见的”。后面会谈到事务的几个隔离级别。

### 持久性Durability
**一旦事务提交，则其所做的修改就会永久保存到数据库中。**此时即使系统崩溃，数据库也能保证你的数据不会丢失。

`One More`一个实现了ACID特性的数据库，相比没有实现ACID的数据库，通常会需要更强的CPU处理能力，更强大的内存和更多的磁盘空间。