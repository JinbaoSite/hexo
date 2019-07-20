---
title: Python机器学习应用 | 多项式回归
date: 2017-06-24 14:16:49
categories: [机器学习]
---
## 1 多项式回归
多项式回归(Polynomial Regression)是研究一个因变量与一个或多个自变量间多项式的回归分析方法。如果自变量只有一个时，称为一元多项式回归；如果自变量有多个时，称为多元多项式回归。
在一元回归分析中，如果依变量y与自变量x的关系为非线性的，但是又找不到适当的函数曲线来拟合，则可以采用一元多项式回归。
多项式回归的最大优点就是可以通过增加x的高次项对实测点进行逼近，直至满意为止。
事实上，多项式回归可以处理相当一类非线性问题，它在回归分析中占有重要的地位，因为任一函数都可以分段用多项式来逼近。
![](http://oltfslql1.bkt.clouddn.com/2pic.jpg)
之前提到的线性回归实例中，是运用直线来拟合数据输入与输出之间的线性关系。不同于线性回归，多项式回归是使用曲线拟合数据的输入与输出的映射关系。

## 2 多项式回归的应用

### 2.1 应用背景

我们在前面已经根据已知的房屋成交价和房屋的尺寸进行了线性回归，继而可以对已知房屋尺寸，而未知房屋成交价格的实例进行了成交价格的预测，但是在实际的应用中这样的拟合往往不够好，因此我们在此对该数据集进行多项式回归。

目标：对房屋成交信息建立多项式回归方程，并依据回归方程对房屋价格进行预测
技术路线：sklearn.preprocessing.PolynomialFeatures

### 2.2 实例数据

为了方便展示，成交信息只使用了房屋的面积以及对应的成交价格。其中：
• 房屋面积单位为平方英尺（ft2）房
• 屋成交价格单位为万
![](http://oltfslql1.bkt.clouddn.com/table.jpg)

### 2.3 实验过程

使用算法：线性回归
实现步骤：
1.建立工程并导入sklearn包
2.加载训练数据，建立回归方程
3.可视化处理

### 2.4 实现步骤

#### 2.4.1 建立工程并导入sklearn包

```
import matplotlib.pyplot as plt
import numpy as np
from sklearn import linear_model
from sklearn.preprocessing import PolynomialFeatures
```

#### 2.4.2 加载训练数据，建立回归方程

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
 
 
poly_reg = PolynomialFeatures(degree = 2)
X_poly = poly_reg.fit_transform(datasets_X)
lin_reg_2 = linear_model.LinearRegression()
lin_reg_2.fit(X_poly, datasets_Y)
```

#### 2.4.3 可视化处理

```
# 图像中显示
plt.scatter(datasets_X, datasets_Y, color = 'red')
plt.plot(X, lin_reg_2.predict(poly_reg.fit_transform(X)), color = 'blue')
plt.xlabel('Area')
plt.ylabel('Price')
plt.show()
```

### 2.5 结果展示

通过多项式回归拟合的曲线与数据点的关系如右图所示。依据该多项式回归方程即可通过房屋的尺寸，来预测房屋的成交价格。
![](http://7xpp0b.com1.z0.glb.clouddn.com/prices22.jpg)