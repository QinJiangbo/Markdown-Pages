---
title: Weka数据集文件格式ARFF
date: 2017-11-28 13:21:22
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/weka-thumbnail.png
tags:
	- Weka
	- 机器学习
	- ARFF
categories:
	- 数据科学
	- 机器学习
keywords:
	- Weka
	- 机器学习
	- ARFF
---
![](https://obrxbqjbi.qnssl.com/blog/image/weka-thumbnail.png)

开始机器学习相关的探索啦！作为一名Java程序员，想学习机器学习相关的技术，那么如何入手呢？有很多选择的，Java也是机器学习非常热门的语言之一，虽然Python是老大。博主决定从Weka入手，逐渐熟习机器学习常用的算法，然后再学习SparkMLLib等。我认为如果想在机器学习领域继续深挖，那么Python的学习是非常有必要的，因为现在很多非常前沿的机器学习相关技术都是先在Python的平台上发布出来，比如Tensorflow等。好啦，废话不多说了，开始进入Weka的学习，第一步就是搞清楚Weka是什么？Weka处理的数据集又是什么？

## Weka是什么？
Weka是一种鸡，叫`新西兰秧鸡`。这种鸟类天生就不具备飞行的能力，而且只在新西兰被发现，所以叫`新西兰秧鸡`。另外，英文`Weka`这个名字的来源也是因为这种鸟的叫声就是这个样子的，weka...weka...weka...，这种鸟类长这个模样：

![Weka](https://obrxbqjbi.qnssl.com/blog/image/weka-animal.png?imageView2/2/w/480/h/300/q/75|imageslim)

好了，言归正传，本文所指的`Weka`其实是一种机器学习的工具包，里面有一系列用于数据挖掘任务的机器学习算法。这些算法可以被直接应用于数据集也可以从你的Java代码中进行调用。`Weka`包含了数据预处理、分类、回归、聚类、关联规则和可视化的一系列工具。而且也非常适合开发出新的机器学习模式。 [官网](https://www.cs.waikato.ac.nz/ml/weka/)原文介绍如下：
> Weka is a collection of machine learning algorithms for data mining tasks. The algorithms can either be applied directly to a dataset or called from your own Java code. Weka contains tools for data pre-processing, classification, regression, clustering, association rules, and visualization. It is also well-suited for developing new machine learning schemes.

## Weka数据集格式-ARFF
说到Weka的学习，它的数据集是跑不掉的，Weka处理的数据集通常是存放在一种叫`ARFF`格式的文件中的。那么，什么是ARFF呢？

### ARFF定义
ARFF全称是Attribute-Relation File Format，属性关系文件格式。ARFF文件是一种描述了一系列具有相同属性的实例的ASCII文本文件。具体一点来说，就是数据集主要是有两部分组成，一部分是属性描述，一部分是数据。属性描述的这一部分也叫**头信息**，一般是放在数据集开始，而数据部分叫**数据信息**，一般是跟在**头信息**的后面。

### ARFF头信息
ARFF的头信息包含这个关系的名称，还有一系列属性以及它们的数据类型。以IRIS数据集([https://archive.ics.uci.edu/ml/datasets/iris](https://archive.ics.uci.edu/ml/datasets/iris))为例，标准的IRIS数据集的头信息如下表示：

``` txt
% source: https://archive.ics.uci.edu/ml/datasets/iris

@RELATION iris

@ATTRIBUTE sepal_length NUMERIC
@ATTRIBUTE sepal_width  NUMERIC
@ATTRIBUTE petal_length NUMERIC
@ATTRIBUTE petal_width  NUMERIC
@ATTRIBUTE class       {Iris-setosa,Iris-versicolor,Iris-virginica}
```
注意到了上面的以`%`开头的那行了吗？那一句是ARFF文件的注释。在ARFF文件中，注释通常都是以`%`开头的。另外`@RELATION`，`@ATTRIBUTE`，`@DATA`都是大小写不敏感的，都可以使用。

#### @RELATION声明
通常来说，`@RELATION`声明应该是在ARFF文件的第一行出现，前面注释啥的不算哈！具体的格式如下：`@RELATION <relation-name>`，这里`<relation-name>`是一个字符串，表示这个关系的名称。如果这个字符串包含空格的话一定要记得加上`空格`，否则识别会出现问题的。另外对于名称也有要求，不要使用`'{', '}', ',' 或者 '%'`开头，或者是在ASCII表中低于`\u0021[!]`的字符。

#### @ATTRIBUTE声明
`@ATTRIBUTE`声明主要是用来指定属性的数据名称和类型，具体的格式如下：`@ATTRIBUTE <attribute-name> <data-type>`，一般来说，`<attribute-name>`的要求和上面`<relation-name>`的要求是一样的，Weka支持的数据格式`<data-type>`主要有以下几种：

+ numeric
+ integer is treated as numeric
+ real is treated as numeric
+ &lt;nominal-specification&gt;
+ string
+ date [&lt;date-format&gt;]
+ relational for multi-instance data (for future use)

其它的几个估计大家挺好明白，而`<nominal-specification>`和`date`估计不太好明白，没关系，我们下面再讲。

##### Nominal属性
所谓的Nominal属性就是后面列举一系列的值，比如`{<nominal-value1>,<nominal-value2>,<nominal-value3>...}`，IRIS数据集的class属性就是这么定义的：`@ATTRIBUTE class       {Iris-setosa,Iris-versicolor,Iris-virginica}`。

##### date属性
date属性的定义比较特殊，因为它还涉及到一个格式的问题，所以我们需要指定相关的格式：`@ATTRIBUTE <attribute-name> date [<date-format>]`。日期的格式是比较复杂的，比如`yyyy-MM-dd`或者是`MM-dd-yyyy`等等，因此需要指定。具体的例子如下：`@ATTRIBUTE birthday DATE "yyyy-MM-dd HH:mm:ss"`对应的可参考的数据集如下：

``` txt
@RELATION Timestamps
 
@ATTRIBUTE timestamp DATE "yyyy-MM-dd HH:mm:ss"
 
@DATA 
"2001-04-03 12:12:12"
"2001-05-03 12:59:55"
```

### ARFF数据信息
ARFF的数据信息主要是一系列的以`,`分隔的数据，并且开头的注解是`@DATA`，还是以IRIS数据集为例，它的标准数据信息如下表示：

``` txt
@DATA
5.1,3.5,1.4,0.2,Iris-setosa
4.9,3.0,1.4,0.2,Iris-setosa
4.7,3.2,1.3,0.2,Iris-setosa
4.6,3.1,1.5,0.2,Iris-setosa
5.0,3.6,1.4,0.2,Iris-setosa
5.4,3.9,1.7,0.4,Iris-setosa
4.6,3.4,1.4,0.3,Iris-setosa
5.0,3.4,1.5,0.2,Iris-setosa
4.4,2.9,1.4,0.2,Iris-setosa
4.9,3.1,1.5,0.1,Iris-setosa
5.4,3.7,1.5,0.2,Iris-setosa
4.8,3.4,1.6,0.2,Iris-setosa
4.8,3.0,1.4,0.1,Iris-setosa
```

#### @DATA声明
ARFF文件的数据部分都是跟在`@DATA`声明后面的。关于数据部分没有什么过多注意的地方，主要是注意数据缺失的情况，比如：

``` txt
@DATA
4.4,?,1.5,?,Iris-setosa
```
可以看到，第二个数据和第四个数据缺失，缺失的数据在ARFF文件中统一使用`?`问号代替。

## 总结
关于ARFF文件的相关理解就是这些，重点是需要知道Weka需要处理什么样的数据文件，以及如何理解并产生这些数据文件。后面会陆续推出一些算法的实际学习过程，都是偏向实战型的，后面会在会用算法解决实际问题的基础上再加深理论上的理解。不过目前如果想要学习理论的同学可以去看看机器学习算法研究相关书籍哈。

`参考文献`：[http://weka.wikispaces.com/ARFF+%28stable+version%29](http://weka.wikispaces.com/ARFF+%28stable+version%29)