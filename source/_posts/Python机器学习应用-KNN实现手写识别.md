---
title: Python机器学习应用 | KNN实现手写识别
date: 2017-06-26 18:08:33
categories: [机器学习]
---
## 1 任务介绍

手写数字识别是一个多分类问题，共有10个分类，每个手写数字图像的类别标签是0~9中的其中一个数。例如下面这三张图片的标签分别是0，1，2。
![](http://oltfslql1.bkt.clouddn.com/012.jpg)
本实例利用sklearn来训练一个K最近邻（k-Nearest Neighbor，KNN）分类器，用于识别数据集DBRHD的手写数字。
比较KNN的识别效果与多层感知机的识别效果。

## 2 KNN的输入

DBRHD数据集的每个图片是一个由0或1组成的32*32的文本矩阵；
KNN的输入为图片矩阵展开的1*1024个神经元。

## 3 KNN手写识别实体构建

本实例的构建步骤如下：
步骤1：建立工程并导入sklearn包
步骤2：加载训练数据
步骤3：构建KNN分类器
步骤4：测试集评价

### 3.1 步骤1：建立工程并导入sklearn包

1）创建sklearnKNN.py文件
2）在sklearnKNN.py文件中导入sklearn相关包
```
import numpy as np     #导入numpy工具包
from os import listdir #使用listdir模块，用于访问本地文件
from sklearn import neighbors
```

### 3.2 步骤2：加载训练数据
1）在sklearnKNN.py文件中，定义img2vector函数，将加载的32*32的图片矩阵展开成一列向量
```
def img2vector(fileName):    
    retMat = np.zeros([1024],int) #定义返回的矩阵，大小为1*1024
    fr = open(fileName)           #打开包含32*32大小的数字文件 
    lines = fr.readlines()        #读取文件的所有行
    for i in range(32):           #遍历文件所有行
        for j in range(32):       #并将01数字存放在retMat中     
            retMat[i*32+j] = lines[i][j]    
    return retMat
```
2）在sklearnKNN.py文件中定义加载训练数据的函数readDataSet。
```
def readDataSet(path):    
    fileList = listdir(path)    #获取文件夹下的所有文件 
    numFiles = len(fileList)    #统计需要读取的文件的数目
    dataSet = np.zeros([numFiles,1024],int)    #用于存放所有的数字文件
    hwLabels = np.zeros([numFiles])#用于存放对应的标签(与神经网络的不同)
    for i in range(numFiles):      #遍历所有的文件
        filePath = fileList[i]     #获取文件名称/路径   
        digit = int(filePath.split('_')[0])   #通过文件名获取标签     
        hwLabels[i] = digit        #直接存放数字，并非one-hot向量
        dataSet[i] = img2vector(path +'/'+filePath)    #读取文件内容 
    return dataSet,hwLabels
```
3）在sklearnKNN.py文件中，调用readDataSet和img2vector函数加载数据，将训练的图片存放在train_dataSet中，对应的标签则存在train_hwLabels中
```
train_dataSet, train_hwLabels = readDataSet('trainingDigits')
```
### 3.3 步骤3：构建KNN分类器

在sklearnKNN.py文件中，构建KNN分类器：设置查找算法以及邻居点数量(k)值。
KNN是一种懒惰学习法，没有学习过程，只在预测时去查找最近邻的点，
数据集的输入就是构建KNN分类器的过程。
构建KNN时我们同时调用了fit函数。
```
knn = neighbors.KNeighborsClassifier(algorithm='kd_tree', n_neighbors=3)
knn.fit(train_dataSet, train_hwLabels)
```

### 3.4 步骤4：测试集评价
1）加载测试集：
```
#read  testing dataSet
dataSet,hwLabels = readDataSet('testDigits')
```
2）使用构建好的KNN分类器对测试集进行预测，并计算预测的错误率
```
res = knn.predict(dataSet)  #对测试集进行预测
error_num = np.sum(res != hwLabels) #统计分类错误的数目
num = len(dataSet)          #测试集的数目
print("Total num:",num," Wrong num:", \
      error_num,"  WrongRate:",error_num / float(num))
```

## 4 实验效果

邻居数量K影响分析：设置K为1、3、5、7的KNN分类器，对比他们的实验效果
| n_neighbors | 1 | 3 | 5 | 7 |
| :--: | :--: | :--: | :--: | :--: | :--: |
| error_num |  13 |  12 |  18 | 22  | 
| RightRate |  0.9863 | 0.9873  |  0.9810 |  0.9767 | 

K=3时正确率最高，当K>3时正确率开始下降，这是由于当样本为稀疏数据集时（本实例只有946个样本），其第k个邻居点可能与测试点距离较远，因此投出了错误的一票进而影响了最终预测结果。

