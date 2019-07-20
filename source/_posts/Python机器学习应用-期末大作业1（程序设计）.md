---
title: Python机器学习应用 | 期末大作业1（程序设计）
date: 2017-07-04 13:09:27
categories: [机器学习]
---
## 1 题目

本次实验为分类任务，实验数据在附件中，共有2个文件，data_train.txt、data_test.txt，分别用于训练和测试，在训练文件中数据有55列，前54列是样本的特征（输入数据），最后一列是样本的类别（label），类别共有7种，对应为1~7。测试数据中没有类别（label），需要根据模型预测相应的类别（label），这些预测结果需要上传，并根据预测结果给出相应的分数。

作业要求：
使用三种模型进行预测。
提交预测结果，每个模型一个文件，命名为model_1.txt、model_2.txt、model_3.txt，文件中每行一个数字（1~7）表示预测结果，行数与data_test.txt的行数相同。
撰写实验报告，包含模型介绍，数据处理，结果展示与分析。
提交预测结果、实验报告、代码。

数据下载：链接：http://pan.baidu.com/s/1crQFH8 密码：tj2p

## 2 数据读取

直接读取data_train.txt文件，先读取全部数据，然后再一行一行读取，这里设置90%用于训练，10%用于验证。
```python
#获取train_data,val_data
f = open('data_train.txt')
lines = f.readlines()

#90%用于训练，10%用于验证
train_len = int(0.9*len(lines))
val_len = len(lines) - train_len

train_dataSet = np.zeros([train_len,54],int)
train_label = np.zeros([train_len,7],int)
val_dataSet = np.zeros([val_len,54],int)
val_label = np.zeros([val_len,7],int)

#训练数据集
for i in range(train_len):
	line = lines[i]
	for j in range(53):
		train_dataSet[i][j] = line.split(' ')[j]
	digit = int(line.split(' ')[54])
	train_label[i][digit-1] = 1.0

#验证数据集
for i in range(val_len):
	line = lines[i+train_len]
	for j in range(53):
		val_dataSet[i][j] = line.split(' ')[j]
	digit = int(line.split(' ')[54])
	val_label[i][digit-1] = 1.0
```
对于测试文件，也是直接读取
```python
#获取test_data.txt
f = open('test_data.txt')
lines = f.readlines()

test_len = len(lines)
test_dataSet = np.zeros([test_len,54],int)

#测试数据集
for i in range(test_len):
	line = lines[i]
	for j in range(53):
		test_dataSet[i][j] = line.split(' ')[j]
```
## 2 MLPClassifier

### 3.1 MLP简介

多层感知机（MLP）是一种全连接神经网络，它包含输入层，隐藏层，输出层。
![](http://oltfslql1.bkt.clouddn.com/20150128033221168.png)

### 3.2 MLP训练和验证

构建神经网络，设置隐藏层神经元个数、激活函数、优化方法、学习率
```
clf = MLPClassifier(hidden_layer_sizes=(200,),activation='logistic', solver='adam',learning_rate_init = 0.0001, max_iter=2000)
```
训练神经网络
```
clf.fit(train_dataSet,train_label)
```
获得训练准确率和验证准确率
```
res = clf.predict(val_dataSet)
cnt = 0
for i in range(val_len):
	if np.sum(res[i]==val_label[i]) == 7:
		cnt += 1

res2 = clf.predict(train_dataSet)
cnt2 = 0
for i in range(train_len):
	if np.sum(res[i]==train_label[i]) == 7:
		cnt2 += 1

print('train accuracy = %.1f,validation accuracy = %.1f (%.1f sec)' %(1.0*cnt2/train_len,1.0*cnt/val_len,time.time()-start_time))
```

### 3.3 结果分析

#### 3.3.1 隐藏层的数目

对隐藏层参数hidden_layer_sizes，分别设置50，100，150，200，250，300进行实验

| hidden_layer_sizes | 50 | 100 | 150 | 200 | 250 | 300 |
| :--: |  :--: | :--: | :--: | :--: | :--: | :--: |
| train accuracy | 73.39% | 76.14% | 77.07% | 78.48% | 79.06% | 79.46% |
| validation accuracy | 73.19% | 75.52% | 76.64% | 77.87% | 78.5% | 78.86% |
| time (sec) | 372.5 | 537.4 | 609.9 | 794.1 | 798.9 | 877.2 |

可以看出，随着隐藏层参数hidden_layer_sizes的增大，train accuracy和validation accuracy都随之增大，但是增长的幅度逐渐降低，训练所花费的时间虽然也在增多，不过后面增长幅度也降低了。所以，这里我们选取200作为合适的隐藏层参数值。

#### 3.3.2 激活函数

激活函数有logistic、identity、tanh、relu，分别设置并进行实验

| activation | 'logistic' | 'identity' | 'tanh' | 'relu' |
| :--: | :--: | :--: | :--: | :--: |
| train accuracy |  78.18%  |  50.27%  |  74.11%  |  56.48%  | 
| validation accuracy |  77.63%  |  50.02%  |  73.82%  |  56.07%  |
| time (sec) |  739.8  |  48.3  |  706.5  |  121.5  |

可以看出选择logistic效果最好

#### 3.3.3 优化器

优化器有sgd、adam，分别设置进行实验

| solver  | 'sgd' | 'adam' |
| :--: | :--: | :--: |
| train accuracy |      25.69%  |  79.20%  |
| validation accuracy |     25.46%  |  78.61%  |
| time (sec) |      67.7  |  904.0  |

很明显，adam优化器效果更好

#### 3.3.4 学习率设置

设置学习率0.1,0.01,0.001,0.0001,0.00001，实验结果如下

| learning_rate_init | 0.1 | 0.01 | 0.001 | 0.0001 | 0.00001 |
| :--: | :--: | :--: | :--: | :--: | :--: |
| train accuracy | 48.71% | 0 | 65.94% | 79.59% | 78.11% |
| validation accuracy | 48.52% |  0 |  65.59% | 78.88% |  77.80%  |
| time (sec) |      51.7  |  50.2  | 117.6 | 965.4 |   3048.1  |

所以设置学习率learning_rate_init=0.0001

### 3.4 获取预测结果

设置隐藏层hidden_layer_sizes=200，激活函数activation='logistic'，优化器为adam，学习率learning_rate_init=0.0001，将所有数据用于训练集（不再设置验证集），并将预测结果输出至文件中。
```
ans = clf.predict(test_dataSet)
f = open('model_1.txt','w')
for i in range(test_len):
	for j in range(7):
		if ans[i][j] != 0:
			f.write(str(j+1) + '\n')
			break
f.close()
```

## 4 KNN

### 4.1 KNN简介

KNN：通过计算待分类数据点与已有数据集中的所有数据点的距离，取距离最小的前K个点，根据“少数服从多数”的原则，将这个数据点划分为出现次数最多的那个类别。

### 4.2 KNN训练和验证

构建KNN分类器：设置查找算法以及邻居点数量(k)值。 
```
knn = neighbors.KNeighborsClassifier(algorithm='kd_tree',n_neighbors=nei)
```
训练神经网络
```
knn.fit(train_dataSet,train_label)
```
获得训练准确率和验证准确率
```
res = knn.predict(val_dataSet)
cnt = 0
for i in range(val_len):
	if np.sum(res[i]==val_label[i]) == 7:
		cnt += 1
res2 = knn.predict(train_dataSet)
cnt2 = 0
for i in range(train_len):
	if np.sum(res2[i]==train_label[i]) == 7:
		cnt2 += 1
print('train accuracy = %.4f,validation accuracy = %.4f (%.1f sec)' %(1.0*cnt2/train_len,1.0*cnt/val_len,time.time()-start_time))
```

### 4.3 结果分析

#### 4.3.1 邻居数量K

设置K=1,2,3,4,5,6,7，实验结果如下：

| n_neighbors | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| train accuracy | 100% | 96.28% | 98.71% | 96.49% | 98.12% | 96.29% | 97.55% |
| validation accuracy | 96.14% | 92.57% | 96.31% | 93.71% | 96.12% | 93.90% | 95.71% |
| time (sec) | 20.1 | 32.2 | 35.9 | 42.1 | 45.5 | 48.3 | 49.2 |

虽然K=1时，训练集准确度最高，但是在验证集中相对更低，所以K=1使得分类器过拟合了，在这里选择K=3作为合适的值。

#### 4.3.2 查找算法

查找算法有：ball_tree、kd_tree、brute，实验结果如下

| algorithm | ball_tree | kd_tree |
| :--: | :--: | :--: |
| train accuracy | 98.71%  |  96.31%  |
| validation accuracy | 98.71% |  96.31% |
| time (sec) | 394.2  |  36.9 |

查找算法ball_tree与kd_tree效果一样，但是ball_tree花费时间是kd_tree的10倍，所以选择kd_tree。

### 4.4 获取预测结果
KNN设置K=3，使用查找算法kd_tree，将全部数据集用于训练，并将预测结果输出至文件中。
```python
ans = knn.predict(test_dataSet)
f = open('model_2.txt','w')
for i in range(test_len):
	for j in range(7):
		if ans[i][j] != 0:
			f.write(str(j+1) + '\n')
			break
f.close()
```

## 5 决策树

### 5.1 决策树简介

决策树是一种树形结构的分类器，通过顺序询问分类点的属性决定分类点最终的类别。通常根据特征的信息增益或其他指标，构建一颗决策树。在分类时，只需要按照决策树中的结点依次进行判断，即可得到样本所属类别。

### 5.2 决策树训练和验证

构建决策树训练器
```
clf = DecisionTreeClassifier()
```
决策树训练
```
clf.fit(train_dataSet,train_label)
```
获得训练准确率和验证准确率
```
res = clf.predict(val_dataSet)
cnt = 0
for i in range(val_len):
	if np.sum(res[i]==val_label[i]) == 7:
		cnt += 1
res2 = clf.predict(train_dataSet)
cnt2 = 0
for i in range(train_len):
	if np.sum(res2[i]==train_label[i]) == 7:
		cnt2 += 1
print('train accuracy = %.4f,validation accuracy = %.4f (%.1f sec)' %(1.0*cnt2/train_len,1.0*cnt/val_len,time.time()-start_time))
```

### 5.3 结果分析

|1 | 2|
| :--: | :--: |
| train accuracy | 100%  | 
| validation accuracy | 92.93% |
| time (sec) | 20.7  |

可以发现决策树存在很明显的过拟合。

### 5.4 获取预测结果
```
ans = clf.predict(test_dataSet)
f = open('model_3.txt','w')
for i in range(test_len):
	for j in range(7):
		if ans[i][j] != 0:
			f.write(str(j+1) + '\n')
			break
f.close()
```
## 5 结果展示与分析

| 分类器 | MLP | KNN | 决策树 |
| :--: | :--: | :--: | :--: |
| train accuracy | 78.80% | 98.79% |  100% |
| time (sec) | 1039.8 | 37.6 | 22.5 |

决策树虽然训练集准确率达到了100%，但是它存在着严重的过拟合，并不是最好的，KNN相对于MLP来说，训练集准确度高，花费时间短，所以，相比之下，KNN的结果应该是最好的。

## 6 代码和数据

链接：http://pan.baidu.com/s/1nv0rLyx 密码：cy8l