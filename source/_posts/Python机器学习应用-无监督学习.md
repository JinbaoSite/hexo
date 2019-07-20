---
title: Python机器学习应用 | 无监督学习
date: 2017-06-27 14:13:16
categories: [机器学习]
---
## 1 无监督学习

利用无标签的数据学习数据的分布或数据与数据之间的关系被称作无监督学习。
有监督学习和无监督学习的最大区别在于数据是否有标签
无监督学习最常应用的场景是聚类(clustering)和降维(DimensionReduction)

## 2 聚类(clustering)

聚类(clustering)，就是根据数据的“相似性”将数据分为多类的过程。
评估两个不同样本之间的“相似性” ，通常使用的方法就是计算两个样本之间的“距离”。使用不同的方法计算样本间的距离会关系到聚类结果的好坏。
![](http://oltfslql1.bkt.clouddn.com/cluster.jpg)

## 3 距离计算

### 3.1 欧氏距离

欧氏距离是最常用的一种距离度量方法，源于欧式空间中两点的距离。其计算方法如下：
$$d = \sqrt{\sum_{n=1}^{k} (x_{1k}-x_{2k})^2}$$

### 3.2 曼哈顿距离

曼哈顿距离也称作“城市街区距离”，类似于在城市之中驾车行驶，从一个十字路口到另外一个十字楼口的距离。其计算方法如下：
$$d = \sum_{n=1}^{k} |x_{1k}-x_{2k}|$$

### 3.3 马氏距离

马氏距离表示数据的协方差距离，是一种尺度无关的度量方式。也就是说马氏距离会先将样本点的各个属性标准化，再计算样本间的距离。其计算方式如下：（s是协方差矩阵，如图）
![](http://oltfslql1.bkt.clouddn.com/dist.jpg)
$$d(x_i,y_j)=\sqrt{(x_i-y_j)^Ts^{-1}(x_i-y_j)}$$

### 3.4 夹角余弦

余弦相似度用向量空间中两个向量夹角的余弦值作为衡量两个样本差异的大小。余弦值越接近1，说明两个向量夹角越接近0度，表明两个向量越相似。其计算方法如下：
$$cos(\theta)=\frac{\sum_{k=1}^{n} x_{1k} x_{2k}}{\sqrt{\sum_{k=1}^{n}x_{1k}^2} \sqrt{\sum_{k=1}^{n}x_{2k}^2}}$$

## 4 Sklearn vs. 聚类

scikit-learn库（以后简称sklearn库）提供的常用聚类算法函数包含在sklearn.cluster这个模块中，如：K-Means，近邻传播算法，DBSCAN，等。
以同样的数据集应用于不同的算法，可能会得到不同的结果，算法所耗费的时间也不尽相同，这是由算法的特性决定的。
![](http://oltfslql1.bkt.clouddn.com/pic1.jpg)![](http://oltfslql1.bkt.clouddn.com/pic2.jpg)

## 5 sklearn.cluster

sklearn.cluster模块提供的各聚类算法函数可以使用不同的数据形式作为输入:
标准数据输入格式:[样本个数，特征个数]定义的矩阵形式。
相似性矩阵输入格式：即由[样本数目，样本数目]定义的矩阵形式，矩阵中的每一个元素为两个样本的相似度，如DBSCAN， AffinityPropagation(近邻传播算法)接受这种输入。如果以余弦相似度为例，则对角线元素全为1. 矩阵中每个元素的取值范围为[0,1]。
![](http://oltfslql1.bkt.clouddn.com/tabletable.jpg)

## 6 降维

降维，就是在保证数据所具有的代表性特性或者分布的情况下，将高维数据转化为低维数据的过程：
- 数据的可视化
- 精简数据
![](http://oltfslql1.bkt.clouddn.com/cl.jpg)

## 7 sklearn vs.降维

- 降维是机器学习领域的一个重要研究内容，有很多被工业界和学术界接受的典型算法，截止到目前sklearn库提供7种降维算法。
- 降维过程也可以被理解为对数据集的组成成份进行分解（decomposition）的过程，因此sklearn为降维模块命名为decomposition, 在对降维算法调用需要使用sklearn.decomposition模块

## 8 sklearn.decomposition

![](http://oltfslql1.bkt.clouddn.com/tabeltable2.jpg)

