---
title: CentOS中Hadoop单机伪分布式配置
date: 2017-12-03 23:48:55
categories: [大数据]
---

## 5 Hadoop单机配置

首先我们需要为我们的主机名增加IP映射，那么我们需要知道我们的主机名是什么，执行下面命令查看主机名
```
vi /etc/sysconfig/network
```
查看一下HOSTNAME的值，你可以修改这个值，也可以直接将这个值拷贝出来
![](http://p06qbh638.bkt.clouddn.com/hostname.png)
执行下面命令，增加IP映射
```
sudo vi /etc/hosts
```
在最后一行增加一行，输入主机的ip，以及上面的HOSTNAME的值
![](http://p06qbh638.bkt.clouddn.com/ip%E6%98%A0%E5%B0%84.png)
接下来运行一个实例来验证hadoop配置成功
```
cd /home/hadoop/hadoop-2.9.0
mkdir ./input
cp ./etc/hadoop/*.xml ./input
./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-2.9.0.jar grep ./input ./output 'dfs[a-z.]+'
cat ./output/*
```
最后运行结果如下
![](http://p06qbh638.bkt.clouddn.com/grepoutput.png)
如果出现错误`INFO metrics.MetricsUtil: Unable to obtain hostName`，说明你ip映射没有成功，需要重新验证你的ip跟主机名是否正确
![](http://p06qbh638.bkt.clouddn.com/unableobtain.png)
重复运行上面的实例需注意，hadoop不会覆盖结果文件，重复运行需先删除原先的结果文件
```
rm -r ./output
```

## 6 Hadoop伪分布式配置

我们首先需要设置Hadoop环境变量
```
vi ~/.bashrc
```
在最后增加如下内容
```
export HADOOP_HOME=/home/hadoop/hadoop-2.9.0
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
```
保存退出后，source一下配置
```
source ~/.bashrc
```
我们还需要修改两个配置文件`core-site.xml`和`hdfs-site.xml`
先修改`core-site.xml`，
```
vi ./etc/hadoop/core-site.xml
```
在`<configuration></configuration>`之间增加如下内容
```
<configuration>
    <property>
        <name>hadoop.tmp.dir</name>
        <value>file:/home/hadoop/hadoop-2.9.0/tmp</value>
        <description>Abase for other temporary directories.</description>
    </property>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
```
然后修改`hdfs-site.xml`,
```
vi ./etc/hadoop/hdfs-site.xml
```
在`<configuration></configuration>`之间增加如下内容
```
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:/home/hadoop/hadoop-2.9.0/tmp/dfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/home/hadoop/hadoop-2.9.0/tmp/dfs/data</value>
    </property>
</configuration>
```
修改完成后，执行NameNode的格式化
```
./bin/hdfs namenode -format
```
成功的话，可以看到`successfully formatted`
![](http://p06qbh638.bkt.clouddn.com/successfullyformatted.png)
接下来开启NameNode和DataNode守护进程
```
./sbin/start-dfs.sh
```
如果出现`Are you sure you want to continue connecting`，输入yes
![](http://p06qbh638.bkt.clouddn.com/datanode.png)
启动完成后，可以通过命令`jps`来判断是否成功，如果出现了`NameNode`、`DataNode`和`SecondaryNameNode`，则说明成功启动，如果`SecondaryNameNode`没有启动，运行`sbin/stop-dfs.sh`关闭进程，然后重新尝试启动，如果没有`NameNode`和`DataNode`，那就是配置不成功，需要仔细检查之间的步骤，或者查看启动日志`/home/hadoop/hadoop-2.9.0/logs/hadoop-hadoop-namenode-dblab.log`排查原因。
![](http://p06qbh638.bkt.clouddn.com/jps.png)
成功启动后，可以访问web界面`ip:80070`查看 NameNode 和 Datanode 信息，还可以在线查看 HDFS 中的文件。
![](http://p06qbh638.bkt.clouddn.com/web.png)

## 7 运行Hadoop伪分布式实例

hadoop单机模式中的实例读取的本地数据，伪分布式读取的是HDFS上的数据，为了使用HDFS，我们需要先在HDFS中创建用户目录，然后再创建input文件夹，并将本地的文件上传至HDFS的`/user/hadoop/input文件夹中
```
./bin/hdfs dfs -mkdir -p /user/hadoop
./bin/hdfs dfs -mkdir input
./bin/hdfs dfs -put ./etc/hadoop/*.xml input
```
中间如果报错，可忽略
![](http://p06qbh638.bkt.clouddn.com/catuch.png)
查看一下HDFS中input的文件列表
```
./bin/hdfs dfs -ls input
```
文件列表如下
![](http://p06qbh638.bkt.clouddn.com/inputfile.png)
接下来运行实例
```
./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-2.9.0.jar grep input output 'dfs[a-z.]+'
```
查看一下HDFS上的结果
```
./bin/hdfs dfs -cat output/*
```
这里我们的input文件更多一些，所以结果也不一样
![](http://p06qbh638.bkt.clouddn.com/hdfsresult.png)
最后，如果想关闭Hadoop，运行下面命令
```
./sbin/stop-dfs.sh
```

## 8 启动YARN

YARN是从MapReduce中分离出来的，负责资源管理与任务调度，我们在这里启动YARN，首先修改配置文件`mapred-site.xml`，先进行重命名
```
mv ./etc/hadoop/mapred-site.xml.template ./etc/hadoop/mapred-site.xml
```
然后修改`mapred-site.xml`，
```
vi ./etc/hadoop/mapred-site.xml
```
在`<configuration></configuration>`增加如下内容
```
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
</configuration>
```
接着修改`yarn-site.xml`，
```
vi ./etc/hadoop/yarn-site.xml
```
在`<configuration></configuration>`增加如下内容
```
<configuration>
<!-- Site specific YARN configuration properties -->
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
```
最后启动YARN
```
./sbin/start-dfs.sh
./sbin/start-yarn.sh
```
![](http://p06qbh638.bkt.clouddn.com/yarnstart.png)
开启历史服务器，方便在web中查看任务运行情况
```
./sbin/mr-jobhistory-daemon.sh start historyserver
```
在浏览器中打开`ip:8088/cluster`
![](http://p06qbh638.bkt.clouddn.com/hadoopcluster.png)
运行命令`jps`，额可以查看到多了NodeManager和ResourceManager两个后台进程
![](http://p06qbh638.bkt.clouddn.com/jpscluster.png)
如果需要关闭，可以执行下面的命令
```
./sbin/stop-yarn.sh
./sbin/mr-jobhistory-daemon.sh stop historyserver
```