---
title: 高性能MySQL之事务隔离级别（四）
date: 2017-08-28 23:50:50
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/high-performance-mysql.png
tags:
	- 事务
	- 隔离级别
categories:
	- 数据科学
	- MySQL
keywords:
	- 事务
	- 隔离级别
---
## 事务概念回顾
这里再回顾一下事务的概念：**事务一组原子性的SQL查询。**事务处理系统一般包括四个特性ACID（原子性Atomicity，一致性Consistency，隔离性Isolation以及持久性Durability）。今天重点聊一聊这里面的隔离性Isolation。

## 隔离级别
隔离性远比想象的要复杂很多，因为涉及到系统各个事务之间的数据更新操作可见性。一般来说，隔离级别会分为四种：

+ READ UNCOMMITTED（读未提交）
+ READ COMMITTED（读已提交）
+ REPEATABLE READ（可重复度）
+ SERIALIZABLE（序列化）

这四种隔离级别的程度从上往下是依次加深的，各个事务操作的可见性逐渐变弱。下面分别说说这四种隔离级别的具体特征：

+ **READ UNCOMMITTED（读未提交）**在这个隔离级别中，任何事务中对数据库修改的操作，对其他事务都是可见的。也就是说，当前事务修改了数据库的某一条数据，还没有提交这个事务的时候，另一个事务就已经能够读取到这个修改后的数据了。这往往不是我们所需要的，因此也容易发生**DIRTY READ（脏读）**。从性能上来说，这个隔离级别并不会比其他的隔离级别高很多，而相反，它带来的数据不一致性问题会造成更大的问题，因此，博主建议一般情况不要在数据库中设置这个隔离级别。
+ **READ COMMITTED（读已提交）**这个隔离级别就是说，一个事务只有将自己提交以后，所做的操作对其他的事务才可见。这很符合我们事务的定义，不是吗？但是往往会存在一个问题就是，在这个隔离级别下，一个事务查询两次执行的结果可能是不一样的。因为可能一个事务查询两次执行的间隙有一个事务提交了，那么这个查询查出来的结果就不一样的。因此这个级别也叫**不可重复度（NON-REPEATABLE READ）**。
+ **REPEATABLE READ（可重复度）**这个隔离级别是相对前一个而言的，该级别保证了同一个事务中的同一个操作多次执行的结果是相同的。这个隔离级别是MySQL的默认隔离级别。但是理论上这个隔离级别还是不能阻止幻读（PHANTOM READ），所谓幻读，**就是某个事务在读取某一范围的数据时，另一个事务在一段数据中添加了一些新的数据，当前事务读出了一些原本没有的数据，就是幻读！**MySQL中InnoDB通过MVVC（多版本并发控制）解决了幻读的问题。
+ **SERIALIZABLE（序列化）**这个是最高的隔离级别，这个很暴力的就是直接在表的每一行加上一个锁。这样能保证每一条数据的更新都是原子性的。但是带来的问题是数据库的性能大大降低。一般不建议使用这个隔离级别，太耗性能了！

## SQL四种隔离级别对比

|隔离级别|脏读可能性|不可重复度可能性|幻读可能性|加锁读可能性|
|-----------------------|--------------|-------------|------------|-----------|
|READ UNCOMMITTED|Yes|Yes|Yes|No|
|READ COMMITTED|No|Yes|Yes|No|
|REPEATABLE READ|No|No|Yes|No|
| SERIALIZABLE|No|No|No|Yes|