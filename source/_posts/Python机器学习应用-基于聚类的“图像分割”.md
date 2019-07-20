---
title: Python机器学习应用 | 基于聚类的“图像分割”
date: 2017-07-01 16:57:29
categories: [机器学习]
---
## 1 图像分割

1、图像分割：利用图像的灰度、颜色、纹理、形状等特征，把图像分成若干个互不重叠的区域，并使这些特征在同一区域内呈现相似性，在不同的区域之间存在明显的差异性。然后就可以将分割的图像中具有独特性质的区域提取出来用于不同的研究。
2、图像分割技术已在实际生活中得到广泛的应用。例如：在机车检验领域，可以应用到轮毂裂纹图像的分割，及时发现裂纹，保证行车安全；在生物医学工程方面，对肝脏CT图像进行分割，为临床治疗和病理学研究提供帮助。

## 2 图像分割常用方法

1.阈值分割：对图像灰度值进行度量，设置不同类别的阈值，达到分割的目的。
2.边缘分割：对图像边缘进行检测，即检测图像中灰度值发生跳变的地方，则为一片区域的边缘。
3.直方图法：对图像的颜色建立直方图，而直方图的波峰波谷能够表示一块区域的颜色值的范围，来达到分割的目的。
4.特定理论：基于聚类分析、小波变换等理论完成图像分割。

## 3 实例描述

### 3.1 目标

利用K-means聚类算法对图像像素点颜色进行聚类实现简单的图像分割

### 3.2 输出

同一聚类中的点使用相同颜色标记，不同聚类颜色不同

### 3.3 技术路线

sklearn.cluster.KMeans

### 3.4 实例数据

数据可以是任意大小的图片，为了使效果更佳直观，可以采用区分度比较明显的图片。

### 3.5 实验过程

使用算法：Kmeans

#### 3.5.1 建立工程并导入sklearn包

```
import numpy as np
import PIL.Image as image
from sklearn.cluster import KMeans
```

#### 3.5.2 加载图片并进行预处理

```
def loadData(filePath):
    f = open(filePath,'rb')
    data = []
    img = image.open(f)
    m,n = img.size
    for i in range(m):
        for j in range(n):
            x,y,z = img.getpixel((i,j))
            data.append([x/256.0,y/256.0,z/256.0])
    f.close()
    return np.mat(data),m,n
 
imgData,row,col = loadData('bull.jpg')
```

#### 3.5.3 加载Kmeans聚类算法

```
label = KMeans(n_clusters=4).fit_predict(imgData)
```
#### 3.5.4 对像素点进行聚类并输出
 
```
label = label.reshape([row,col])
pic_new = image.new("L", (row, col))
for i in range(row):
    for j in range(col):
        pic_new.putpixel((i,j), int(256/(label[i][j]+1)))
pic_new.save("result-bull-4.jpg", "JPEG")
```

### 3.6 结果展示

原图
![](http://7xpp0b.com1.z0.glb.clouddn.com/1.jpg)
K=4
![](http://7xpp0b.com1.z0.glb.clouddn.com/2.jpg)