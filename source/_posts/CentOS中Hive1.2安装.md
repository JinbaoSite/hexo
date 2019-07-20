---
title: CentOS中Hive1.2安装
date: 2018-05-06 15:54:23
categories: [大数据]
---

## 1 安装Hive

安装Hive之前必须先安装Hadoop
1、[CentOS安装Hadoop](https://www.dongjinbao.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/CentOS%E5%AE%89%E8%A3%85Hadoop.html)
2、[CentOS中Hadoop单机伪分布式配置](https://www.dongjinbao.com/%E5%A4%A7%E6%95%B0%E6%8D%AE/CentOS%E4%B8%ADHadoop%E5%8D%95%E6%9C%BA%E4%BC%AA%E5%88%86%E5%B8%83%E5%BC%8F%E9%85%8D%E7%BD%AE.html)

### 1.1 下载Hive

这里提供清华大学镜像下载链接[hive-1.2.2](https://mirrors.tuna.tsinghua.edu.cn/apache/hive/hive-1.2.2/)
将其通过Xftp工具上传到云主机上，执行下面的命令解压
```
tar -zxvf apache-hive-1.2.2-bin.tar.gz
mv apache-hive-1.2.2-bin.tar.gz hive
```

### 1.2 配置环境变量

我们将环境变量配置到`~/.bashrc`文件中去，先对文件进行编辑
```
vi ~/.bashrc
```
然后添加环境变量
```
export HIVE_HOME=/home/dongjinbao/bigdata/hive
export PATH=$PATH:$HIVE_HOME/bin
```
其中`HIVE_HOME`就是你下载解压`apache-hive-1.2.2-bin.tar.gz`的目录
![配置Hive环境变量](http://7xwian.com1.z0.glb.clouddn.com/hive-conf.png)

### 1.4 配置Hive配置文件

#### 1.4.1 配置`hive-default.xml`

在`hive`文件夹下，找到`conf`目录，将其中的`hive-default.xml.template`重命名为`hive-default.xml`即可
```
cp hive-default.xml.template hive-default.xml
```

#### 1.4.2 配置`hive-site.xml`

在`hive\conf`目录下创建`hive-site.xml`,
```
touch hive-site.xml
```
并对这个文件进行编辑
```
vi hive-site.xml
```
添加下面内容
```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
  <property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true</value>
    <description>JDBC connect string for a JDBC metastore</description>
  </property>
  <property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.jdbc.Driver</value>
    <description>Driver class name for a JDBC metastore</description>
  </property>
  <property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>hive</value>
    <description>username to use against metastore database</description>
  </property>
  <property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>hive</value>
    <description>password to use against metastore database</description>
  </property>
</configuration>
```
这里我们采用mysql元数据库，所以接下来我们需要安装mysql。

## 2 安装mysql

### 2.1 mysql安装

使用yum安装MySQL
```
sudo yum install -y mysql-server mysql mysql-devel
```
安装完成后，启动MySQL服务
```
sudo service mysqld restart
```
设置MySQL账户root密码
```
/usr/bin/mysqladmin -u root password 'RUqkYc3y'
```
最后我们登录一下MySQL服务器
```
mysql -u root -p
```
输入root用户密码后，如果成功进入mysql，则说明安装成功。

### 2.2 为Hive配置MySQL

新建hive数据库
```
mysql> create database hive;
```
配置mysql允许hive接入
```
mysql> grant all on *.* to hive@localhost identified by 'hive';
mysql> flush privileges;
```
下载[mysql-connector-java-5.1.40-bin.jar](http://central.maven.org/maven2/mysql/mysql-connector-java/5.1.40/mysql-connector-java-5.1.40.jar)，并放入`hive/lib`目录下

## 3 验证安装

执行`hive`命令启动
```
hive
```
在hive中执行sql语句
```
hive> show databases;
```
无报错，则说明Hive安装成功！
![Hive执行sql语句](http://7xwian.com1.z0.glb.clouddn.com/hive.png)