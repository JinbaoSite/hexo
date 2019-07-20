---
title: Python机器学习应用 | 聚类——DBSCAN方法及应用
date: 2017-06-27 15:03:00
categories: [机器学习]
---
## 1 DBSCAN密度聚类

DBSCAN算法是一种基于密度的聚类算法：
• 聚类的时候不需要预先指定簇的个数
• 最终的簇的个数不定

DBSCAN算法将数据点分为三类：
• 核心点：在半径Eps内含有超过MinPts数目的点
• 边界点：在半径Eps内点的数量小于MinPts，但是落在核心点的邻域内
• 噪音点：既不是核心点也不是边界点的点

![](http://oltfslql1.bkt.clouddn.com/dbscan.jpg)

## 2 DBSCAN算法流程

1.将所有点标记为核心点、边界点或噪声点；
2.删除噪声点；
3.为距离在Eps之内的所有核心点之间赋予一条边；
4.每组连通的核心点形成一个簇；
5.将每个边界点指派到一个与之关联的核心点的簇中（哪一个核心点的半径范围之内）。

有如下13个样本点，使用DBSCAN进行聚类
![](http://oltfslql1.bkt.clouddn.com/db.jpg)
取Eps=3，MinPts=3，依据DBSACN对所有点进行聚类（曼哈顿距离）。
![](http://oltfslql1.bkt.clouddn.com/eps.jpg)
对每个点计算其邻域Eps=3内的点的集合。
集合内点的个数超过MinPts=3的点为核心点
查看剩余点是否在核心点的邻域内，若在，则为边界点，否则为噪声点。
![](http://oltfslql1.bkt.clouddn.com/min.jpg)
将距离不超过Eps=3的点相互连接，构成一个簇，核心点邻域内的点也会被加入到这个簇中。则右侧形成3个簇。
![](http://oltfslql1.bkt.clouddn.com/mini.jpg)

## 3 DBSCAN的应用实例

### 3.1 数据介绍

现有大学校园网的日志数据，290条大学生的校园网使用情况数据，数据包括用户ID，设备的MAC地址，IP地址，开始上网时间，停止上网时间，上网时长，校园网套餐等。利用已有数据，分析学生上网的模式。

### 3.2 实验目的

通过DBSCAN聚类，分析学生上网时间和上网时长的模式。
技术路线：sklearn.cluster.DBSCAN

### 3.3 数据实例

![](http://oltfslql1.bkt.clouddn.com/stu.jpg)

### 3.4 实验过程

使用算法： DBSCAN聚类算法
![](http://oltfslql1.bkt.clouddn.com/gong.jpg)

#### 3.4.1 建立工程，导入sklearn相关包

```
import numpy as np
import sklearn.cluster as skc
from sklearn import metrics
import matplotlib.pyplot as plt
```

#### 3.4.2 读入数据并进行处理

```
mac2id=dict()
onlinetimes=[]
f=open('TestData.txt',encoding='utf-8')
for line in f:
    mac=line.split(',')[2]
    onlinetime=int(line.split(',')[6])
    starttime=int(line.split(',')[4].split(' ')[1].split(':')[0])
    if mac not in mac2id:
        mac2id[mac]=len(onlinetimes)
        onlinetimes.append((starttime,onlinetime))
    else:
        onlinetimes[mac2id[mac]]=[(starttime,onlinetime)]
real_X=np.array(onlinetimes).reshape((-1,2))
```

#### 3.4.3 上网时间聚类，创建DBSCAN算法实例，并进行训练，获得标签

```
X=real_X[:,0:1]
 
db=skc.DBSCAN(eps=0.01,min_samples=20).fit(X)
labels = db.labels_
```

#### 3.4.4 输出标签，查看结果

```
print('Labels:')
print(labels)
raito=len(labels[labels[:] == -1]) / len(labels)
print('Noise raito:',format(raito, '.2%'))
 
n_clusters_ = len(set(labels)) - (1 if -1 in labels else 0)
 
print('Estimated number of clusters: %d' % n_clusters_)
print("Silhouette Coefficient: %0.3f"% metrics.silhouette_score(X, labels))
 
for i in range(n_clusters_):
    print('Cluster ',i,':')
    print(list(X[labels == i].flatten()))
```
结果如下：
![](http://oltfslql1.bkt.clouddn.com/ans.jpg)

#### 3.4.5 画直方图，分析实验结果

```
plt.hist(X,24)
plt.show()
```
![](http://oltfslql1.bkt.clouddn.com/figure_1.jpeg)
上网时间大多聚集在22：00和23：00
