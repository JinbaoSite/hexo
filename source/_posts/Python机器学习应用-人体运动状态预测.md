---
title: Python机器学习应用 | 人体运动状态预测
date: 2017-06-23 19:02:50
categories: [机器学习]
---
## 1 背景介绍

可穿戴式设备的流行，让我们可以更便利地使用传感器获取人体的各项数据，甚至生理数据。
当传感器采集到大量数据后，我们就可以通过对数据进行分析和建模，通过各项特征的数值进行用户状态的判断，根据用户所处的状态提供给用户更加精准、便利的服务。

## 2 数据介绍

我们现在收集了来自 A,B,C,D,E 5位用户的可穿戴设备上的传感器数据，每位用户的数据集包含一个特征文件（a.feature）和一个标签文件（a.label）。
特征文件中每一行对应一个时刻的所有传感器数值，标签文件中每行记录了和特征文件中对应时刻的标记过的用户姿态，两个文件的行数相同，相同行之间互相对应。

### 2.1 数据介绍-feature

我们的特征文件共包含41列特征，数据内容如下图
![](http://oltfslql1.bkt.clouddn.com/feature.jpg)
特征文件的各项特征具体如下表所示
![](http://oltfslql1.bkt.clouddn.com/feature1.jpg)
在传感器1对应的13列数据特征中，包含：1项温度数据、3项一型三轴加速度数据、3项二型三轴加速度数据、3项三轴陀螺仪数据和3项三轴磁场数据。
![](http://oltfslql1.bkt.clouddn.com/feature2.jpg)
人体的温度数据可以反映当前活动的剧烈程度，一般在静止状态时，体温趋于稳定在36.5度上下；当温度高于37度时，可能是进行短时间的剧烈运动，比如跑步和骑行。
![](http://oltfslql1.bkt.clouddn.com/feature3.jpg)
在数据中有两个型号的加速度传感器，可以通过互相印证的方式，保证数据的完整性和准确性。通过加速度传感器对应的三个数值，可以知道空间中x、y、z三个轴上对应的加速度，而空间上的加速度和用户的姿态有密切的关系，比如用户向上起跳时，z轴上的加速度会激增。
![](http://oltfslql1.bkt.clouddn.com/feature4.jpg)
陀螺仪是角运动检测的常用仪器，可以判断出用户佩戴传感器时的身体角度是水平、倾斜还是垂直。直观地，通过这些数值都是推断姿态的重要指标。
![](http://oltfslql1.bkt.clouddn.com/feature5.jpg)
磁场传感器可以检测用户周围的磁场强度和数值大小，这些数据可以帮助我们理解用户所处的环境。比如在一个办公场所，用户座位附近的磁场是大体上固定的，当磁场发生改变时，我们可以推断用户的位置和场景发生了变化。
![](http://oltfslql1.bkt.clouddn.com/feature6.jpg)

### 2.2 数据介绍-label

标签文件内容如图所示，每一行代表与特征文件中对应行的用户姿态类别。总共有0-24共25种身体姿态，如，无活动状态，坐态、跑态等。标签文件作为训练集的标准参考准则，可以进行特征的监督学习。
![](http://oltfslql1.bkt.clouddn.com/feature7.jpg)

## 3 任务介绍

假设现在出现了一个新用户，但我们只有传感器采集的数据，那么该如何得到
这个新用户的姿态呢？
又或者对同一用户如果传感器采集了新的数据，怎么样根据新的数据判断当前
用户处于什么样的姿态呢？

## 4 算法流程
需要从特征文件和标签文件中将所有数据加载到内存中，由于存在缺失值，此步骤还需要进行简单的数据预处理。
创建对应的分类器，并使用训练数据进行训练。
利用测试集预测，通过使用真实值和预测值的比对，计算模型整体的准确率和召回率，来评测模型。
```flow
st=>start: 开始
io=>inputoutput: 加载数据&预处理
op1=>operation: 创建分类器
op2=>operation: 训练分类器
op3=>operation: 在测试集上得到预测结果
op4=>operation: 计算准确率和召回率
e=>end: 结束
st->io->op1->op2->op3->op4->e
```

## 5 模块导入

导入numpy库和pandas库
从sklearn库中导入预处理模块Imputer
导入自动生成训练集和测试集的模块train_test_split
导入预测结果评估模块classification_report
```
import numpy as np
import pandas as pd

from sklearn.preprocessing import Imputer
from sklearn.model_selection import train_test_split
from sklearn.metrics import classification_report

from sklearn.neighbors import KNeighborsClassifier
from sklearn.tree import DecisionTreeClassifier
from sklearn.naive_bayes import GaussianNB
```
接下来，从sklearn库中依次导入三个分类器模块：K近邻分类器KNeighborsClassifier、决策树分类器DecisionTreeClassifier和高斯朴素贝叶斯函数GaussianNB。

## 6 数据导入函数
```
def load_datasets(feature_paths, label_paths):
    feature = np.ndarray(shape=(0,41))
    label = np.ndarray(shape=(0,1))
    for file in feature_paths:
        df = pd.read_table(file, delimiter=',', na_values='?', header=None)
        imp = Imputer(missing_values='NaN', strategy='mean', axis=0)
        imp.fit(df)
        df = imp.transform(df)
        feature = np.concatenate((feature, df))
     
    for file in label_paths:
        df = pd.read_table(file, header=None)
        label = np.concatenate((label, df))
         
    label = np.ravel(label)
    return feature, label
```
编写数据导入函数，设置传入两个参数，分别是特征文件的列表feature_paths和标签文件的列表label_paths。
定义feature数组变量，列数量和特征维度一致为41；定义空的标签变量，列数量与标签维度一致为1。
使用pandas库的read_table函数读取一个特征文件的内容，其中指定分隔符为逗号、缺失值为问号且文件不包含表头行。
使用Imputer函数，通过设定strategy参数为‘mean’，使用平均值对缺失数据进行补全。fit()函数
用于训练预处理器，transform()函数用于生成预处理结果。
将预处理后的数据加入feature，依次遍历完所有特征文件
遵循与处理特征文件相同的思想，我们首先使用pandas库的read_table函数读取一个标签文件的内容，
其中指定分隔符为逗号且文件不包含表头行。
由于标签文件没有缺失值，所以直接将读取到的新数据加入label集合，依次遍历完所有标签文件，得到标签集合label。
最后函数将特征集合feature与标签集合label返回。

## 7 主函数-数据准备
```
if __name__ == '__main__':
    ''' 数据路径 '''
    featurePaths = ['A/A.feature','B/B.feature','C/C.feature','D/D.feature','E/E.feature']
    labelPaths = ['A/A.label','B/B.label','C/C.label','D/D.label','E/E.label']
    ''' 读入数据  '''
    x_train,y_train = load_datasets(featurePaths[:4],labelPaths[:4])
    x_test,y_test = load_datasets(featurePaths[4:],labelPaths[4:])
    x_train, x_, y_train, y_ = train_test_split(x_train, y_train, test_size = 0.0)
```
设置数据路径feature_paths和label_paths。
使用python的分片方法，将数据路径中的前4个值作为训练集，并作为参数传入load_dataset()函数中，得到训练集合的特征x_train，训练集的标签y_train。
将最后一个值对应的数据作为测试集，送入load_dataset()函数中，得到测试集合的特征x_test，测试集的标签y_test。
使用train_test_split()函数，通过设置测试集比例test_size为0，将数据随机打乱，便于后续分类器的初始化和训练。

### 7.1 主函数-knn
```
    print('Start training knn')
    knn = KNeighborsClassifier().fit(x_train, y_train)
    print('Training done')
    answer_knn = knn.predict(x_test)
    print('Prediction done')
```
使用默认参数创建K近邻分类器，并将训练集x_train和y_train送入fit()函数进行训练，训练后的分类器保存到变量knn中。
使用测试集x_test，进行分类器预测，得到分类结果answer_knn。

### 7.2 主函数-决策树
```
    print('Start training DT')
    dt = DecisionTreeClassifier().fit(x_train, y_train)
    print('Training done')
    answer_dt = dt.predict(x_test)
    print('Prediction done')
```
使用默认参数创建决策树分类器dt，并将训练集x_train和y_train送入fit()函数进行训练。训练后的分类器保存到变量dt中。
使用测试集x_test，进行分类器预测，得到分类结果answer_dt。

### 7.3 主函数-贝叶斯
```  
    print('Start training Bayes')
    gnb = GaussianNB().fit(x_train, y_train)
    print('Training done')
    answer_gnb = gnb.predict(x_test)
    print('Prediction done')
```
使用默认参数创建贝叶斯分类器，并将训练集x_train和y_train送入fit()函数进行训练。训练后的分类器保存到变量gnb中。
使用测试集x_test，进行分类器预测，得到分类结果answer_gnb。

### 7.4 主函数-分类结果
```
    print('\n\nThe classification report for knn:')
    print(classification_report(y_test, answer_knn))
    print('\n\nThe classification report for DT:')
    print(classification_report(y_test, answer_dt))
    print('\n\nThe classification report for Bayes:')
    print(classification_report(y_test, answer_gnb))
```
使用classification_report函数对分类结果，从精确率precision、召回率recall、f1值f1-score和支持度support四个维度进行衡量。
分别对三个分类器的分类结果进行输出

## 8 结果展示
k近邻
![](http://oltfslql1.bkt.clouddn.com/knn2.jpg)
决策树
![](http://oltfslql1.bkt.clouddn.com/ctree.jpg)
贝叶斯
![](http://oltfslql1.bkt.clouddn.com/gnb.jpg)
结果对比
![](http://oltfslql1.bkt.clouddn.com/QQ%E6%88%AA%E5%9B%BE20170623185952.jpg)
结论：
从准确度的角度衡量，贝叶斯分类器的效果最好
从召回率和F1值的角度衡量，k近邻效果最好
贝叶斯分类器和k近邻的效果好于决策树

## 附录
数据：链接: http://pan.baidu.com/s/1jHHcjA2 密码: xjg6
完整程序
```
import pandas as pd
import numpy as np  
 
from sklearn.preprocessing import Imputer
from sklearn.cross_validation import train_test_split 
from sklearn.metrics import classification_report
   
from sklearn.neighbors import KNeighborsClassifier
from sklearn.tree import DecisionTreeClassifier
from sklearn.naive_bayes import GaussianNB
 
def load_datasets(feature_paths, label_paths):
    feature = np.ndarray(shape=(0,41))
    label = np.ndarray(shape=(0,1))
    for file in feature_paths:
        df = pd.read_table(file, delimiter=',', na_values='?', header=None)
        imp = Imputer(missing_values='NaN', strategy='mean', axis=0)
        imp.fit(df)
        df = imp.transform(df)
        feature = np.concatenate((feature, df))
     
    for file in label_paths:
        df = pd.read_table(file, header=None)
        label = np.concatenate((label, df))
         
    label = np.ravel(label)
    return feature, label
 
if __name__ == '__main__':
    ''' 数据路径 '''
    featurePaths = ['A/A.feature','B/B.feature','C/C.feature','D/D.feature','E/E.feature']
    labelPaths = ['A/A.label','B/B.label','C/C.label','D/D.label','E/E.label']
    ''' 读入数据  '''
    x_train,y_train = load_datasets(featurePaths[:4],labelPaths[:4])
    x_test,y_test = load_datasets(featurePaths[4:],labelPaths[4:])
    x_train, x_, y_train, y_ = train_test_split(x_train, y_train, test_size = 0.0)
     
    print('Start training knn')
    knn = KNeighborsClassifier().fit(x_train, y_train)
    print('Training done')
    answer_knn = knn.predict(x_test)
    print('Prediction done')
     
    print('Start training DT')
    dt = DecisionTreeClassifier().fit(x_train, y_train)
    print('Training done')
    answer_dt = dt.predict(x_test)
    print('Prediction done')
     
    print('Start training Bayes')
    gnb = GaussianNB().fit(x_train, y_train)
    print('Training done')
    answer_gnb = gnb.predict(x_test)
    print('Prediction done')
     
    print('\n\nThe classification report for knn:')
    print(classification_report(y_test, answer_knn))
    print('\n\nThe classification report for DT:')
    print(classification_report(y_test, answer_dt))
    print('\n\nThe classification report for Bayes:')
    print(classification_report(y_test, answer_gnb))
```
