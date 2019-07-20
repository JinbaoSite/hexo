---
title: Python机器学习应用 | 线性回归
date: 2017-06-24 11:00:48
categories: [机器学习]
---
## 1 线性回归

线性回归(Linear Regression)是利用数理统计中回归分析，来确定两种或两种以上变量间相互依赖的定量关系的一种统计分析方法。
线性回归利用称为线性回归方程的最小平方函数对一个或多个自变量和因变量之间关系进行建模。这种函数是一个或多个称为回归系数的模型参数的线性组合。只有一个自变量的情况称为简单回归,大于一个自变量情况的叫做多元回归。

线性回归：使用形如$y=w^Tx+b$的线性模型拟合数据输入和输出之间的映射关系的。
![](http://oltfslql1.bkt.clouddn.com/l.jpg)

## 2 线性回归的实际用途

线性回归有很多实际的用途，分为以下两类：
1.如果目标是预测或者映射，线性回归可以用来对观测数据集的y和X的值拟合出一个预测模型。当完成这样一个模型以后，对于一个新增的X值，在没有给定与它相配对的y的情况下，可以用这个拟合过的模型预测出一个y值。
2.给定一个变量y和一些变量$X_1,...,X_p$,这些变量有可能与y相关，线性回归分析可以用来量化y与$X_j$之间相关性的强度，评估出与y不相关的$X_j$，并识别出哪些$X_j$的子集包含了关于y的冗余信息。

## 3 线性回归的应用

### 3.1 背景

与房价密切相关的除了单位的房价，还有房屋的尺寸。我们可以根据已知的房屋成交价和房屋的尺寸进行线性回归，继而可以对已知房屋尺寸，而未知房屋成交价格的实例进行成交价格的预测。

目标：对房屋成交信息建立回归方程，并依据回归方程对房屋价格进行预测
技术路线：sklearn.linear_model.LinearRegression

### 3.2 实例数据

为了方便展示，成交信息只使用了房屋的面积以及对应的成交价格。其中：
• 房屋面积单位为平方英尺（ft2）房
• 屋成交价格单位为万
![](http://oltfslql1.bkt.clouddn.com/table.jpg)

### 3.3 可行性分析

简单而直观的方式是通过数据的可视化直接观察房屋成交价格与房屋尺寸间是否存在线性关系。
对于本实验的数据来说，散点图就可以很好的将其在二维平面中进行可视化表示。

下图为数据的散点图，其中横坐标为房屋面积，纵坐标为房屋的成交价格。可以看出，靠近坐标左下角部分的点，表示房屋尺寸较小的房子，其对应的房屋成交价格也相对较低。同样的，靠近坐标右上部分的点对应于大尺寸高价格的房屋。从总体来看，房屋的面积和成交价格基本成正比。
![](http://oltfslql1.bkt.clouddn.com/2table.jpg)

### 3.4 实验过程

使用算法：线性回归
实现步骤：
1.建立工程并导入sklearn包
2.加载训练数据，建立回归方程
3.可视化处理

### 3.5 实现步骤

#### 3.5.1 建立工程并导入sklearn包

```
import matplotlib.pyplot as plt
import numpy as np
from sklearn import linear_model
```

#### 3.5.2 加载训练数据，建立回归方程

```
# 读取数据集
datasets_X = []
datasets_Y = []
fr = open('prices.txt','r')
lines = fr.readlines()
for line in lines:
    items = line.strip().split(',')
    datasets_X.append(int(items[0]))
    datasets_Y.append(int(items[1]))
 
length = len(datasets_X)
datasets_X = np.array(datasets_X).reshape([length,1])
datasets_Y = np.array(datasets_Y)

minX = min(datasets_X)
maxX = max(datasets_X)
X = np.arange(minX,maxX).reshape([-1,1])

linear = linear_model.LinearRegression()
linear.fit(datasets_X, datasets_Y)
```

#### 3.5.3 可视化处理

```
# 图像中显示
plt.scatter(datasets_X, datasets_Y, color = 'red')
plt.plot(X, linear.predict(X), color = 'blue')
plt.xlabel('Area')
plt.ylabel('Price')
plt.show()
```

### 3.6 结果展示

通过回归方程拟合的直线与原有数据点的关系如右图所示，依据该回归方程即可通过房屋的尺寸，来预测房屋的成交价格。
![](http://oltfslql1.bkt.clouddn.com/prices.jpg)