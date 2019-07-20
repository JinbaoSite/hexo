---
title: Python机器学习应用 | 降维——PCA方法及其应用
date: 2017-06-29 19:10:53
categories: [机器学习]
---
## 1 主成分分析（PCA）

1、主成分分析（Principal Component Analysis，PCA）是最常用的一种降维方法，通常用于高维数据集的探索与可视化，还可以用作数据压缩和预处理等。
2、PCA可以把具有相关性的高维变量合成为线性无关的低维变量，称为主成分。主成分能够尽可能保留原始数据的信息。
3、矩阵的主成分就是其协方差矩阵对应的特征向量，按照对应的特征值大小进行排序，最大的特征值就是第一主成分，其次是第二主成分，以此类推。

## 2 算法过程

输入：样本集$D=${$x_1,x_2,...,x_m$};低维空间维数$d'$
过程：
1、对所有样本进行中心化：$x_1 \leftarrow x_i - \frac{1}{m} \sum_{i=1}^{m} x_i$;
2、计算样本的协方差矩阵$XX^T$;
3、对协方差矩阵$XX^T$做特征值分解;
4、取最大的$d'$个特征值所对应的特征值$w_1,w_2,...,w_{d'}$;
输出：投影矩阵$W = (w_1,w_2,...,w_{d'})$。

## 3 sklearn中主成分分析

在sklearn库中，可以使用sklearn.decomposition.PCA加载PCA进行降维，主要参数有：
• n_components：指定主成分的个数，即降维后数据的维度
• svd_solver ：设置特征值分解的方法，默认为‘auto’,其他可选有‘full’, ‘arpack’, ‘randomized’。

## 4 PCA实现高维数据可视化

### 4.1 背景

目标：已知鸢尾花数据是4维的，共三类样本。使用PCA实现对鸢尾花数据进行降维，实现在二维平面上的可视化。
![](http://oltfslql1.bkt.clouddn.com/yah.jpg)

### 4.2 程序编写

#### 4.2.1 建立工程，导入sklearn相关工具包：

```
>>> import matplotlib.pyplot as plt
#加载matplotlib用于数据的可视化
>>> from sklearn.decomposition import PCA
#加载PCA算法包
>>> from sklearn.datasets import load_iris
#加载鸢尾花数据集导入函数

```

#### 4.2.2 加载数据并进行降维

```
>>> data = load_iris()
#以字典形式加载鸢尾花数据集
>>> y = data.target #使用y表示数据集中的标签
>>> X = data.data #使用X表示数据集中的属性数据
>>> pca = PCA(n_components=2)
#加载PCA算法，设置降维后主成分数目为2
>>> reduced_X = pca.fit_transform(X)
#对原始数据进行降维，保存在reduced_X中
```

#### 4.2.3 按类别对降维后的数据进行保存

```
>>> red_x, red_y = [], []
#第一类数据点
>>> blue_x, blue_y = [], []
#第二类数据点
>>> green_x, green_y = [], []
#第三类数据点
>>> for i in range(len(reduced_X)):
>>>     if y[i] == 0:
>>>         red_x.append(reduced_X[i][0])
>>>         red_y.append(reduced_X[i][1])
>>>     elif y[i] == 1:
>>>         blue_x.append(reduced_X[i][0])
>>>         blue_y.append(reduced_X[i][1])
>>>     else:
>>>         green_x.append(reduced_X[i][0])
>>>         green_y.append(reduced_X[i][1])
```

#### 4.2.4 降维后数据点的可视化

```
>>> plt.scatter(red_x, red_y, c='r', marker='x')
#第一类数据点
>>> plt.scatter(blue_x, blue_y, c='b', marker='D')
#第二类数据点
>>> plt.scatter(green_x, green_y, c='g', marker='.')
#第三类数据点
>>> plt.show()
#可视化
```

### 4.3 结果展示

![](http://oltfslql1.bkt.clouddn.com/yanhua.jpg)
可以看出，降维后的数据仍能够清晰地分成三类。这样不仅能削减数据的维度，降低分类任务的工作量，还能保证分类的质量。