---
title: Python机器学习应用 | 聚类——K-means方法及应用
date: 2017-06-27 14:41:06
categories: [机器学习]
---
## 1 K-means聚类算法

k-means算法以k为参数，把n个对象分成k个簇，使簇内具有较高的相似度，而簇间的相似度较低。其处理过程如下：
1.随机选择k个点作为初始的聚类中心；
2.对于剩下的点，根据其与聚类中心的距离，将其归入最近的簇
3.对每个簇，计算所有点的均值作为新的聚类中心
4.重复2、3直到聚类中心不再发生改变
![](http://oltfslql1.bkt.clouddn.com/julei.jpg)

## 2 K-means的应用

### 2.1 数据介绍

现有1999年全国31个省份城镇居民家庭平均每人全年消费性支出的八个主要变量数据，这八个变量分别是：食品、衣着、家庭设备用品及服务、医疗保健、交通和通讯、娱乐教育文化服务、居住以及杂项商品和服务。利用已有数据，对31个省份进行聚类。

### 2.2 实验目的

通过聚类，了解1999年各个省份的消费水平在国内的情况。
技术路线：sklearn.cluster.Kmeans

### 2.3 数据实例
![](http://oltfslql1.bkt.clouddn.com/datatable.jpg)

### 2.4 实验过程

使用算法： K-means聚类算法

#### 2.4.1 建立工程，导入sklearn相关包

```
import numpy as np
from sklearn.cluster import KMeans
```

#### 2.4.2 加载数据，创建K-means算法实例，并进行训练，获得标签
1.利用loadData方法读取数据
2.创建实例
3.调用Kmeans()、fit_predict()方法进行计算
```
def loadData(filePath):
    fr = open(filePath,'r+')
    lines = fr.readlines()
    retData = []
    retCityName = []
    for line in lines:
        items = line.strip().split(",")
        retCityName.append(items[0])
        retData.append([float(items[i]) for i in range(1,len(items))])
    return retData,retCityName

if __name__ == '__main__':
    data,cityName = loadData('city.txt')
    km = KMeans(n_clusters=4)
    label = km.fit_predict(data)
    expenses = np.sum(km.cluster_centers_,axis=1)
    #print(expenses)
    CityCluster = [[],[],[],[]]
    for i in range(len(cityName)):
        CityCluster[label[i]].append(cityName[i])
    for i in range(len(CityCluster)):
        print("Expenses:%.2f" % expenses[i])
        print(CityCluster[i])
```
调用KMeans方法所需参数：
• n_clusters：用于指定聚类中心的个数
• init：初始聚类中心的初始化方法
• max_iter：最大的迭代次数
• 一般调用时只用给出n_clusters即可，init默认是k-means++，max_iter默认是300

其它参数：
• data：加载的数据
• label：聚类后各数据所属的标签
• axis: 按行求和
• fit_predict()：计算簇中心以及为簇分配序号

#### 2.4.3 输出标签，查看结果

- 将城市按照消费水平n_clusters类，消费水平相近的城市聚集在一类中
- expense：聚类中心点的数值加和，也就是平均消费水平

聚成2类：km = KMeans(n_clusters=2)
```
Expenses:4040.42
['河北', '山西', '内蒙古', '辽宁', '吉林', '黑龙江', '江苏', '安徽', '江西', '山东', '河南', '湖南', '湖北', '广西', '海南', '四川', '贵州', '云南', '陕西', '甘肃', '青海', '宁夏', '新疆']
Expenses:6457.13
['北京', '天津', '上海', '浙江', '福建', '广东', '重庆', '西藏']
```
聚成3类：km = KMeans(n_clusters=3)
```
Expenses:3827.87
['河北', '山西', '内蒙古', '辽宁', '吉林', '黑龙江', '安徽', '江西', '山东', '河南', '湖北', '贵州', '陕西', '甘肃', '青海', '宁夏', '新疆']
Expenses:5113.54
['天津', '江苏', '浙江', '福建', '湖南', '广西', '海南', '重庆', '四川', '云南', '西藏']
Expenses:7754.66
['北京', '上海', '广东']
```
聚成4类：km = KMeans(n_clusters=4)
```
Expenses:4441.04
['安徽', '湖南', '湖北', '广西', '海南', '四川', '云南']
Expenses:7754.66
['北京', '上海', '广东']
Expenses:3788.76
['河北', '山西', '内蒙古', '辽宁', '吉林', '黑龙江', '江西', '山东', '河南', '贵州', '陕西', '甘肃', '青海', '宁夏', '新疆']
Expenses:5567.33
['天津', '江苏', '浙江', '福建', '重庆', '西藏']
```
从结果可以看出消费水平相近的省市聚集在了一类，例如消费最高的“北京”“上海”“广东”聚集在了消费最高的类别。聚4类时，结果可以比较明显的看出消费层级。