---
title: CentOS安装Hadoop
date: 2017-12-03 12:43:33
categories: [大数据]
---
## 1 创建hadoop用户

首先以root用户登录远程服务器，执行下面命令创建新用户hadoop，并修改hadoop用户的密码
```
useradd -m hadoop -s /bin/bash
passwd hadoop
```
![](http://p06qbh638.bkt.clouddn.com/%E5%88%9B%E5%BB%BAhadoop.png)
为了避免出现权限问题，我们为hadoop用户增加管理员权限
```
visudo
```
找到第98行，就是`root ALL=(ALL) ALL`这行，可以先按`ESC`键，然后输入`:98`，可以直接跳到第98行
我们在这行下面增加一行内容，中间的间隔为Tab
```
hadoop	ALL=(ALL) ALL
```
先按下`i`，进入编辑模式后，加入上面一行内容，编辑完成后，先按`ESC`键，输入`:wq`，按回车键保存退出
![](http://p06qbh638.bkt.clouddn.com/zengjiaquanxian.png)

## 2 安装和配置SSH无密码登录

新开一个窗口，以hadoop用户登录进来，然后执行下列命令检查CentOS是否已安装了SSH client、SSH server
```
rpm -qa | grep ssh
```
如果返回的结果包含了SSH client和SSH server，那么无需安装，如下图
![](http://p06qbh638.bkt.clouddn.com/%E6%A3%80%E6%9F%A5%E5%AE%89%E8%A3%85.png)
若需要安装，可执行下面命令安装
```
sudo yum install openssh-clients
sudo yum install openssh-server
```
接下来，我们测试一下SSH是否可用
```
ssh localhost
```
首次登录SSH需要输入yes，然后按照提示输入hadoop密码
![](http://p06qbh638.bkt.clouddn.com/sshcheck.png)
为了减少每次登陆SSH输入密码，我们配置SSH为无密码登录
先退出SSH，执行`exit`命令
![](http://p06qbh638.bkt.clouddn.com/exit.png)
进入`.ssh`目录下，然后利用ssh-keygen生成密钥
```
cd /home/hadoop/.ssh/
ssh-keygen -t rsa
```
遇到所有的提示，全部按回车键
![](http://p06qbh638.bkt.clouddn.com/sshkeygen.png)
最后将密钥加入到授权中去，并修改文件权限
```
cat id_rsa.pub >> authorized_keys
chmod 600 ./authorized_keys
```
![](http://p06qbh638.bkt.clouddn.com/catchmod.png)
在试一下登录ssh命令`ssh localhost`命令，就可以无需密码直接登陆
![](http://p06qbh638.bkt.clouddn.com/localhostssh.png)

## 3 安装Java环境

先使用xftp将[jdk-8u151-linux-x64.tar.gz](http://download.oracle.com/otn-pub/java/jdk/8u151-b12/e758a0de34e24606bca991d704f6dcbf/jdk-8u151-linux-x64.tar.gz?AuthParam=1512228017_7899e64b7f2c6b96948ff371d9427160)文件放入`/home/hadoop`目录下
通过xshell在`/home/hadoop`目录下新建java文件夹
```
sudo mkdir /home/hadoop/java
```
然后将压缩包拷贝到java文件夹下
```
sudo cp jdk-8u151-linux-x64.tar.gz /home/hadoop/java
```
进入`/home/hadoop/java`目录
```
cd /home/hadoop/java
```
然后解压`jdk-8u151-linux-x64.tar.gz`
```
sudo tar zxvf jdk-8u151-linux-x64.tar.gz
```
接下来设置jdk环境变量，这里采用全局设置方法，修改`~/.bashrc`
```
vi ~/.bashrc
```
添加下面内容
```
JAVA_HOME=/home/hadoop/java/jdk1.8.0_151

JRE_HOME=/home/hadoop/java/jdk1.8.0_151/jre

CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib

PATH=$JAVA_HOME/bin:$PATH

export PATH JAVA_HOME CLASSPATH
```
![](http://p06qbh638.bkt.clouddn.com/hadoopjava.png)
最后source一下
```
source ~/.bashrc
```
验证一下
```
java -version
```
![](http://p06qbh638.bkt.clouddn.com/javaversion.png)

## 4 安装Hadoop

先下载[hadoop-2.9.0.tar.gz](https://mirrors.cnnic.cn/apache/hadoop/common/stable/hadoop-2.9.0.tar.gz)，通过xftp上传到`/home/hadoop`目录下
解压`hadoop-2.9.0.tar.gz`
```
sudo tar zxvf hadoop-2.9.0.tar.gz
```
修改一下文件的权限
```
sudo chown -R hadoop:hadoop hadoop-2.9.0
```
![](http://p06qbh638.bkt.clouddn.com/%E4%BF%AE%E6%94%B9%E6%9D%83%E9%99%90.png)
验证一下Hadoop是否安装成功
```
./hadoop-2.9.0/bin/hadoop version
```
结果如下则说明安装成功
![](http://p06qbh638.bkt.clouddn.com/hadoopcheck.png)
