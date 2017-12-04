---
title: Weka机器学习实战之KMeans
date: 2017-12-02 15:29:58
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/weka-thumbnail.png
tags:
	- Weka
	- 机器学习
	- Kmeans
categories:
	- 数据科学
	- 机器学习
keywords:
	- Weka
	- 机器学习
	- Kmeans
---
本文重点讲述如何使用Weka API中的Kmeans算法进行实践。这一篇是所有机器学习文章里面的第一篇，因此选择的是Kmeans算法来进行实践。废话不多说，开始吧！

## 实践环境
这里的环境主要是介绍使用的Maven包，pom文件如下：

``` xml
<dependencies>
    <dependency>
        <groupId>nz.ac.waikato.cms.weka</groupId>
        <artifactId>weka-stable</artifactId>
        <version>3.8.0</version>
    </dependency>
    <dependency>
        <groupId>com.google.guava</groupId>
        <artifactId>guava</artifactId>
        <version>22.0</version>
    </dependency>
</dependencies>
```
选择使用Google的Guava包是因为我们在载入数据集的时候会需要Guava工具包帮我们从classpath（这里是resources目录）下面直接载入进来。

## 准备数据集
针对Kmeans算法，我选择的是比较经典的IRIS数据集。下载地址是：[https://archive.ics.uci.edu/ml/datasets/iris](https://archive.ics.uci.edu/ml/datasets/iris)。部分片段如下：

``` txt
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
```

可以看到它是一种csv（Comma Separated Value）文件。但是我们前面讲到过，使用Weka处理数据的时候通常需要对它进行一下转换，转换为arff文件。因此，我们需要对数据集进行预处理。预处理的过程非常简单，由于数据集不大，所以直接在数据集里面添加一些元信息即可。

``` txt
@RELATION iris

@ATTRIBUTE sepal_length NUMERIC
@ATTRIBUTE sepal_width  NUMERIC
@ATTRIBUTE petal_length NUMERIC
@ATTRIBUTE petal_width  NUMERIC
@ATTRIBUTE class       {Iris-setosa,Iris-versicolor,Iris-virginica}

@DATA
5.1,3.5,1.4,0.2,Iris-setosa
4.9,3.0,1.4,0.2,Iris-setosa
4.7,3.2,1.3,0.2,Iris-setosa
4.6,3.1,1.5,0.2,Iris-setosa
5.0,3.6,1.4,0.2,Iris-setosa
5.4,3.9,1.7,0.4,Iris-setosa
4.6,3.4,1.4,0.3,Iris-setosa
```

## Kmeans算法实践
完整的代码如下：

``` java
package com.qinjiangbo.algorithms.kmeans;

import com.google.common.io.Resources;
import weka.clusterers.SimpleKMeans;
import weka.core.Instances;
import weka.core.converters.ArffLoader;

import java.io.File;
import java.io.IOException;
import java.net.URL;

/**
 * @date: 27/11/2017 6:58 PM
 * @author: qinjiangbo@github.io
 * @description:
 *      使用KMeans算法将Iris数据集进行聚类
 */
public class KmeansCluster {

    /**
     * 训练数据集文件地址
     */
    private static final String TRAINING_DATASET_FILENAME = "kmeans/iris.arff";

    public static void main(String[] args) {
        try {
            // 进行聚类
            process();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 加载数据集
     * @param fileName 文件路径
     * @return
     */
    public static Instances loadDataSet(String fileName) {
        Instances instances = null;
        URL url = Resources.getResource(fileName);
        File file = new File(url.getPath());
        ArffLoader loader = new ArffLoader();
        try {
            loader.setFile(file);
            instances = loader.getDataSet();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return instances;
    }

    /**
     * 操作数据集
     * @throws Exception
     */
    public static void process() throws Exception {
        SimpleKMeans kMeans = generateClassifier();
        // 打印聚类结果
        System.out.println(kMeans.preserveInstancesOrderTipText());
        System.out.println(kMeans.toString());
    }

    /**
     * 训练生成分类器
     * @return
     * @throws Exception
     */
    public static SimpleKMeans generateClassifier() throws Exception {
        Instances instances = loadDataSet(TRAINING_DATASET_FILENAME);
        // 初始化聚类器
        SimpleKMeans kMeans = new SimpleKMeans();
        // 设置聚类要得到的簇数
        kMeans.setNumClusters(3);
        // 开始进行聚类
        kMeans.buildClusterer(instances);
        return kMeans;
    }

}

```

关于代码中的步骤我就不一一介绍了，因为注释已经解释的很详细了，不过一定要记住操作的顺序：

+ 加载数据集
+ 初始化聚类器对象
+ 开始聚类
+ 打印聚类结果

## Kmeans运行结果
将以上的代码拷贝到你的编辑器中，编译执行你就能看到以下结果：

``` txt
Preserve order of instances.

kMeans
======

Number of iterations: 3
Within cluster sum of squared errors: 7.817456892309574

Initial starting points (random):

Cluster 0: 6.1,2.9,4.7,1.4,Iris-versicolor
Cluster 1: 6.2,2.9,4.3,1.3,Iris-versicolor
Cluster 2: 6.9,3.1,5.1,2.3,Iris-virginica

Missing values globally replaced with mean/mode

Final cluster centroids:
                                          Cluster#
Attribute                Full Data               0               1               2
                           (150.0)          (50.0)          (50.0)          (50.0)
==================================================================================
sepal_length                5.8433           5.936           5.006           6.588
sepal_width                  3.054            2.77           3.418           2.974
petal_length                3.7587            4.26           1.464           5.552
petal_width                 1.1987           1.326           0.244           2.026
class                  Iris-setosa Iris-versicolor     Iris-setosa  Iris-virginica
```

## 总结
如果你得到了上面的运行结果，说明你的代码运行完全正确。通过本文章的学习，我相信你已经掌握了Weka中基本的数据集处理及训练流程，后面再结合其它的算法能更加深入地理解机器学习的`套路`。