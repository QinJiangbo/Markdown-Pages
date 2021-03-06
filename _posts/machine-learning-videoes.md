---
title: （转）机器学习相关视频
date: 2016-10-06 21:42:02
thumbnail: https://obrxbqjbi.qnssl.com/blog/image/machine_learning.png
tags:
	- 机器学习
	- Mechine Learning
categories:
	- 数据科学
	- 机器学习
keywords:
	- 机器学习
	- Machine Learning
---
**原文地址：**  [http://liliphd.iteye.com/blog/1929358](http://liliphd.iteye.com/blog/1929358)

近日，在网易公开课视频网站上看完了《机器学习》课程视频，现做个学后感，也叫观后感吧。 

## 学习时间 
从2013年7月26日星期五开始，在网易公开课视频网站上，观看由斯坦福大学Andrew Ng教授主讲的计算机系课程（编号CS229）《机器学习》（网址[http://v.163.com/special/opencourse/machinelearning.html](http://v.163.com/special/opencourse/machinelearning.html)）（注：最早是在新浪公开课上发现的这门课，看了前几集没有字幕的视频。后来经由技术群网友的指引才找到网易，看到了全部翻译完的视频）。我基本上每天看1-2集，不熟悉的内容会在第二天复习一遍。到2013年8月17日全部视频看完，前后用了23天，中间有几天有事或者脑子不在状态就没看。全部看完之后，又找自己感兴趣的重看，我翻看了第11集的内容，“对开发机器学习应用的建议”，老师根据自己的实际项目经验提出了很好的建议，对我们的实战有很大的帮助。 

## 课程设置和内容 
视频课程分为20集，每集72-85分钟。实体课程大概一周2次，中间还穿插助教上的习题课，大概一个学期的课程。 

内容涉及四大部分，分别是：监督学习（2-8集）、学习理论（9集-11集）、无监督学习（12-15集）、强化学习（16-20集）。监督学习和无监督学习，基本上是机器学习的二分法；强化学习位于两者之间；而学习理论则从总体上介绍了如何选择、使用机器学习来解决实际问题，以及调试（比如：误差分析、销蚀分析）、调优（比如：模型选择、特征选择）的各种方法和要注意的事项（比如，避免过早优化）。 

监督学习，介绍了回归、朴素贝叶斯、神经网络、SVM（支持向量机）、SMO（顺序最小优化）算法等；无监督学习讲了聚类、K-means、GMM（混合高斯模型）、EM算法 、PCA（主成分分析）、LSI（潜在语义索引）、SVD（奇异值分解）、ICA（独立成分分析）等；强化学习主要讲了这类连续决策学习（马尔科夫决策过程，MDP）中的值迭代（VI）和策略迭代（PI），以及如何定义回报函数，如何找到最佳策略等问题。 

## 授课方式 
网上有老师的讲义，可以在网易这门课的主页面上打包下载（网址[http://v.163.com/special/opencourse/machinelearning.html](http://v.163.com/special/opencourse/machinelearning.html)）。老师基本上是写板书的，PPT是辅助。在黑板上用粉笔边讲解边书写，有助于带动学生的思考，使师生之间有交流有互动。个人以为，比直接显示PPT效果好。数学公式的推导很费时间，课堂上也不可能大多数的时间用来推导公式，所以大量的推导老师要求学生在课下看讲义，或者通过习题课听助教讲解。 

## 授课语言 
因为是美国的课堂，当然的教学语言是英语。网易做的不错，除了把老师说的话全部转写下来，还做了中文翻译，前14集翻译得不错，除了偶有错别字之外，专业术语翻译的很好，语句也很流畅。第15集以后一直到最后一堂课，翻译的不是太准确，一些专业术语都翻译错了，很让观者感到不适。但是，无论如何，还是感谢网易这些转写和翻译的无名网友无私的付出。这些小的瑕疵不会让真正热爱这门课程的学习者放弃学习，反而想加入翻译者的队伍，为传播科学知识而贡献力量呢。 

## 观后感 
总体感觉，老师讲的不错，是个真正懂机器学习的人。老师在课上也说过，很容易区分那些真正懂机器学习的人，和那些只会纸上谈兵的人。我希望成为第一类，并为此努力着。 

老师是华裔，中文名字叫吴恩达，生于伦敦，看上去很亲切。课堂很活跃，老师注重和学生交流，每讲完一个主题，会问学生有问题吗，然后一一作答。 

视频大概录制于2007年（个人推测，未经考证），内容上，与现在的机器学习技术比，稍微显得不够多。近年来，机器学习领域有了长足的发展，学术界和工业界齐发力，二者相互促进，达到了前所未有的高度。即便是曾经沉寂的神经网络，近年来也改头换面成了深度学习。不过，从专家的角度看，这不是一种新的机器学习技术，它只是涉及到其中的一个环节——特征选择，并不构成一个独立的学习方法。 

老师没有涉及实战。受限于课堂讲授的方式和时间上的限制，课上只能做必要知识点的讲解。 

数学公式比较多，似懂非懂的。如果不满足于“知其然”，还要“知其所以然”，以后的方向是搞模型、算法研究的话，那还要补习一下数学知识，必须的。如果仅是为了解决实际问题，对算法要求不高的话，那知道如何运用就够了。剩下的，随着应用系统的不断进展，对整个系统各方面要求的提高，那时会倒逼你进阶的。 

遗憾的是，因为没有完全掌握，所以再回看已经看过的视频，还是似懂非懂，但是比第一次要好很多。建议大家多看几遍，加强练习，跟自己的项目相结合，动手实现会加深理解。“精通的目的全在于应用”（毛语），机器学习只是工具，应用到解决实际问题上才能真正体现它的价值。 

跟这个课程最接近的，是加州理工学院的《机器学习与数据挖掘》（18集）（网址[http://v.163.com/special/opencourse/learningfromdata.html](http://v.163.com/special/opencourse/learningfromdata.html)），主讲老师有口音，很重，如果没有中文字幕的帮助，很难快速掌握。目前网易的进展是，翻译完了前4集。 

顺便说一句，以后想练专业口语的话，可以多看Andrew Ng这个，跟着说，以后在国际会议上就能充分表达了。听加州理工的这个，也能听懂那些非英语母语国家讲的英语了。不同的地方有不同的英语口音，我们还不算难听的，应该算是好听的，呵呵。 

又及，自己心里暗想，土鳖也能“准”“海归”一回。网络带来了革命，网络也给我们这些爱学习的人带来了真正“免费的午餐”。其实，话说回来，就像免费的搜索引擎一样，他们收获的是更大的名声上的胜利，扩大了影响，传播了美誉。像耶鲁大学的一个教授的一句玩笑话，其目的是争取“世界学术霸权”。 

**Andrew Ng教授的《机器学习》公开课视频（30集）**
http://openclassroom.stanford.edu/MainFolder/CoursePage.php?course=MachineLearning 

**Andrew Ng教授的Deep Learning维基，有中文翻译**
http://deeplearning.stanford.edu/wiki/index.php/UFLDL_Tutorial 

## 其他教学资源 
**韩家炜教授在北大的《数据挖掘》暑期班视频，英文PPT，中文讲解（22集）** 
[http://v.youku.com/v_show/id_XMzA3NDI5MzI=.html](http://v.youku.com/v_show/id_XMzA3NDI5MzI=.html)（视频：01数据挖掘概念，课程简介，数据库技术发展史，数据挖掘应用） 

**韩家炜教授（UIUC大学）的《数据挖掘》在线课程**
[https://wiki.engr.illinois.edu/display/cs412/Home;jsessionid=6BF0A2C36A95A31D2DA754A017756F4B](https://wiki.engr.illinois.edu/display/cs412/Home;jsessionid=6BF0A2C36A95A31D2DA754A017756F4B) 

**卡内基•梅隆大学（CMU）的《机器学习》在线课程**
[http://www.cs.cmu.edu/~epxing/Class/10701/lecture.html](http://www.cs.cmu.edu/~epxing/Class/10701/lecture.html) 

**麻省理工学院（MIT）的《机器学习》在线课程**
[http://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-867-machine-learning-fall-2006/index.htm](http://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-867-machine-learning-fall-2006/index.htm) 

**加州理工学院（Caltech）的《机器学习与数据挖掘》在线课程**
[http://work.caltech.edu/telecourse.html](http://work.caltech.edu/telecourse.html)（同上述网易公开课)
[http://v.163.com/special/opencourse/learningfromdata.html](http://work.caltech.edu/telecourse.html（同上述网易公开课http://v.163.com/special/opencourse/learningfromdata.html)

**UC Irvine的《机器学习与数据挖掘》在线课程**
[http://sli.ics.uci.edu/Classes/2011W-178](http://sli.ics.uci.edu/Classes/2011W-178) 

**斯坦福大学的《数据挖掘》在线课程**
[http://www.stanford.edu/class/stats202/](http://www.stanford.edu/class/stats202/) 

## 其他资源 
**北京机器学习读书会** 
[http://q.weibo.com/1644133](http://q.weibo.com/1644133) 

**机器学习相关电子书** 
[http://t.cn/zjtPuCS](http://t.cn/zjtPuCS)（打开artificial intelligence找子目录machine learning） 

附： 
主讲教师介绍：（新浪公开课：机器学习http://open.sina.com.cn/course/id_280/） 
讲师：Andrew Ng 
学校：斯坦福 
斯坦福大学计算机系副教授，人工智能实验室主任，致力于人工智能、机器学习，神经信息科学以及机器人学等研究方向。他和他的学生成功开发出新的机器视觉算法，大大简化了机器人的传感器系统。 