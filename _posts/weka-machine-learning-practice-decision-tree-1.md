---
title: Weka机器学习实战之决策树1
date: 2017-12-04 21:39:18
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/weka-thumbnail.png
tags:
	- Weka
	- 机器学习
	- 决策树
categories:
	- 数据科学
	- 机器学习
keywords:
	- Weka
	- 机器学习
	- 决策树
---
之前读了周志华的《机器学习》，学习了决策树这一章的相关内容，觉得收获很大。尤其是作者提出的这个数据集，以它为例进行了非常精彩的论述。本文决定也采用《机器学习》书上的例子---西瓜数据集。

## 数据集介绍
西瓜数据集是作者自己独创的一个数据集，也是书的封面插图。数据集可以描述如下：

### 训练集
``` csv
青绿,蜷缩,浊响,清晰,凹陷,硬滑,是
乌黑,蜷缩,沉闷,清晰,凹陷,硬滑,是
乌黑,蜷缩,浊响,清晰,凹陷,硬滑,是
青绿,稍蜷,浊响,清晰,稍凹,软粘,是
乌黑,稍蜷,浊响,稍糊,稍凹,软粘,是
青绿,硬挺,清脆,清晰,平坦,软粘,否
浅白,稍蜷,沉闷,稍糊,凹陷,硬滑,否
乌黑,稍蜷,浊响,清晰,稍凹,软粘,否
浅白,蜷缩,浊响,模糊,平坦,硬滑,否
青绿,蜷缩,沉闷,稍糊,稍凹,硬滑,否
```

### 测试集
``` csv
青绿,蜷缩,沉闷,清晰,凹陷,硬滑,是
浅白,蜷缩,浊响,清晰,凹陷,硬滑,是
乌黑,稍蜷,浊响,清晰,稍凹,硬滑,是
乌黑,稍蜷,沉闷,稍糊,稍凹,硬滑,否
浅白,硬挺,清脆,模糊,平坦,硬滑,否
浅白,蜷缩,浊响,模糊,平坦,软粘,否
青绿,稍蜷,浊响,稍糊,凹陷,硬滑,否
```

## 数据集处理
关于数据集的处理，还是和我们之前的套路一样，需要将它转换为Weka能够处理的格式---arff文件。所以，我们需要在文件上加上这一个头，以便于Weka中的决策树实现J48训练器能够识别这个数据集。

### 数据集头部
``` txt
@RELATION watermelon

@ATTRIBUTE 色泽 {青绿,乌黑,浅白}
@ATTRIBUTE 根蒂 {蜷缩,稍蜷,硬挺}
@ATTRIBUTE 敲声 {浊响,沉闷,清脆}
@ATTRIBUTE 纹理 {清晰,稍糊,模糊}
@ATTRIBUTE 脐部 {凹陷,稍凹,平坦}
@ATTRIBUTE 触感 {硬滑,软粘}
@ATTRIBUTE class {是,否}
```

注意数据部分前面别忘记添加`@DATA`啦。

### 关于属性类型为String报错的问题
我之前的写法是下面这样的：

``` txt
@RELATION watermelon

@ATTRIBUTE 色泽 String
@ATTRIBUTE 根蒂 String
@ATTRIBUTE 敲声 String
@ATTRIBUTE 纹理 String
@ATTRIBUTE 脐部 String
@ATTRIBUTE 触感 String
@ATTRIBUTE class {是,否}
```

结果报错说不能识别String类型的属性，我就很郁闷了[老鸟别喷~]，排查了很久才发现这个地方应该是Nominal类型，也就是分类类型。其实大家可以看到这些属性的值都是在几个值之间切换的，所以它们是相对固定的，切记不要写成了String类型。

## 训练模型
模型的训练我们使用的是J48分类器实现的，J48是是C4.5算法在Weka中的实现。针对ID3算法的不足做了很多优化。具体使用代码如下：

``` java
package com.qinjiangbo.algorithms.decisiontree;

import com.google.common.collect.Lists;
import com.google.common.io.Resources;
import weka.classifiers.Classifier;
import weka.classifiers.Evaluation;
import weka.classifiers.trees.J48;
import weka.core.DenseInstance;
import weka.core.Instance;
import weka.core.Instances;
import weka.core.converters.ArffLoader;

import java.io.File;
import java.io.IOException;
import java.net.URL;
import java.util.List;
import java.util.Random;

/**
 * @date: 03/12/2017 9:52 PM
 * @author: qinjiangbo@github.io
 * @description:
 *      本类主要用于学习《机器学习》西瓜数据集的处理和预测
 */
public class WatermelonClassifier {
    /**
     * 训练数据集和测试数据集是相同的，用于后面交叉验证
     */
    private static final String TRAINING_DATASET_FILENAME = "decisiontree/watermelon-training.arff";
    private static final String TESTING_DATASET_FILENAME = "decisiontree/watermelon-test.arff";

    public static void main(String[] args) {
        try {
            Instances instances = loadDataSet(TRAINING_DATASET_FILENAME);
            // 青绿,蜷缩,沉闷,清晰,凹陷,硬滑 [是]
            // 浅白,蜷缩,浊响,模糊,平坦,软粘 [否]
            List<String> data =
                    Lists.newArrayList("浅白","蜷缩","浊响","模糊","平坦","软粘");
            // 进行预测
            String classOfData = predict(data, instances);
            System.out.println("class of data is: " + classOfData);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 加载数据集
     * @param fileName 训练集文件地址
     * @return
     */
    public static Instances loadDataSet(String fileName) {
        Instances instances = null;
        try {
            URL url = Resources.getResource(fileName);
            File file = new File(url.getPath());
            ArffLoader arffLoader = new ArffLoader();
            arffLoader.setFile(file);
            instances = arffLoader.getDataSet();
        } catch (IOException e) {
            e.printStackTrace();
        }
        instances.setClassIndex(instances.numAttributes()-1);
        return instances;
    }

    /**
     * 训练生成分类器
     * @return
     */
    public static Classifier generateClassifier() throws Exception{
        Instances instances = loadDataSet(TRAINING_DATASET_FILENAME);
        // 初始化分类器
        Classifier j48 = new J48();
        // 训练该数据集
        j48.buildClassifier(instances);
        return j48;
    }

    /**
     * 打印出当前数据最可能所属的类别
     * @return
     */
    public static String predict(List<String> data, Instances trainingSet) throws Exception{
        Classifier j48 = generateClassifier();
        // 创建Instance
        Instance instance = new DenseInstance(trainingSet.numAttributes());
        // 分别添加待预测特征值
        for (int i = 0; i < data.size(); i++) {
            instance.setValue(trainingSet.attribute(i), data.get(i));
        }
        // 需要能访问数据集
        instance.setDataset(trainingSet);
        // 得出最可能所属类别
        int index = (int)j48.classifyInstance(instance);
        return trainingSet.classAttribute().value(index);
    }
}
```

## 测试模型
选择了两个实例来对模型进行测试，其实上面的代码已经给出了，分别是`青绿,蜷缩,沉闷,清晰,凹陷,硬滑 [是]`和`浅白,蜷缩,浊响,模糊,平坦,软粘 [否]`。

### 第一组测试
数据实例：`青绿,蜷缩,沉闷,清晰,凹陷,硬滑 [是]`，输出结果如下：

``` txt
class of data is: 是
```

### 第二组测试
数据实例：`浅白,蜷缩,浊响,模糊,平坦,软粘 [否]`，输出结果如下：

``` txt
class of data is: 否
```

## 总结
这个例子比较简单，主要是利用决策树对数据进行预测和分类，从这个实践的过程能学习到数据的预处理和一些评价的标准，比如准确率和召回率等等，关于这两部分后面的文章中我会进行对应地介绍。机器学习能帮助我们解决很多我们常规方式解决不了的东西。