---
title: HiveQL表
date: 2018-05-14 22:29:46
categories: [大数据]
---

## 1 表的分类

在Hive中，表一般分为管理表（内部表）、外部表、分区表、外部分区表

#### 1.1 管理表（内部表）

一般情况下，安装SQL语言创建的表都是管理表，也称为内部表，这些表一般不与其他工作共享数据，当我们删除表的时候就会自动删除其表里的数据。创建方式与SQL语言一致
```
CREATE TABLE employee(
  name STRING,
  id STRING,
  age INT
);
```

#### 1.2 外部表

外部表指的是将数据存储在外部，可以与其他工作共享数据，删除表的时候只会删除其存储的元数据，而不会删除表里的数据，在创建外部表的时候会额外增加`EXTENAL`关键字，用于告诉Hive这是一张外部表。
```
CREATE EXTENAL TABLE employee(
  name STRING,
  id STRING,
  age INT
);
```
