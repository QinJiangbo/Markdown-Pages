---
title: Language Model Tools Usage of SRILM
date: 2016-08-12 23:43:21
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/english-thumbnail.png
tags: 
	- SRILM
	- NLP
categories: 
	- 开发技术
	- Java
keywords: [NLP,SRILM]
---

一、小数据

假设有去除特殊符号的训练文本trainfile.txt，以及测试文本testfile.txt，那么训练一个语言模型以及对其进行评测的步骤如下：
<!--more-->

1：词频统计

``` bash
  ngram-count -text trainfile.txt -order 3 -write trainfile.count
```

  其中-order 3为3-gram，trainfile.count为统计词频的文本

2：模型训练

``` bash
  ngram-count -read trainfile.count -order 3 -lm trainfile.lm  -interpolate -kndiscount
```

  其中trainfile.lm为生成的语言模型，-interpolate和-kndiscount为插值与折回参数

3：测试（困惑度计算）

``` bash
 ngram -ppl testfile.txt -order 3 -lm trainfile.lm -debug 2 > file.ppl
```

 其中testfile.txt为测试文本，-debug 2为对每一行进行困惑度计算，类似还有-debug 0 , -debug 1, -debug 3等，最后  将困惑度的结果输出到file.ppl。

二、大数据（BigLM）

对于大文本的语言模型训练不能使用上面的方法，主要思想是将文本切分，分别计算，然后合并。步骤如下：

1：切分数据

``` bash
  split -l 10000 trainfile.txt filedir/
```

  即每10000行数据为一个新文本存到filedir目录下。

2：对每个文本统计词频

``` bash
  make-bath-counts filepath.txt 1 cat ./counts -order 3
```

  其中filepath.txt为切分文件的全路径，可以用命令实现：ls $(echo $PWD)/* > filepath.txt，将统计的词频结果存放在counts目录下

3：合并counts文本并压缩

``` bash
  merge-batch-counts ./counts
```

  不解释

4：训练语言模型

``` bash
  make-big-lm -read ../counts/*.ngrams.gz -lm ../split.lm -order 3
```

 用法同ngram-counts

5: 测评（计算困惑度）

``` bash
ngram -ppl filepath.txt -order 3 -lm split.lm -debug 2 > file.ppl
```