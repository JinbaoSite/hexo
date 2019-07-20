---
title: Maven添加引入外部jar包
date: 2018-03-02 23:19:24
categories: [Java]
---
有时候你或许会想在你的项目中加入你自己开发的jar包，或者引入一些Maven库中没有的jar包，例如连接Oracle JDBC的ojdbc6，由于权限问题，现在Maven库中已经无法加载这个jar包

Maven添加引入外部jar包，个人觉得比较好的一种方式是将外部jar打入本地Maven仓库
首先先确保安装有Maven，在cmd中运行`mvn -v`，如果显示了Maven的安装目录，则说明Maven安装成功，否则，你需要重新安装Maven。
![](http://7xpp0b.com1.z0.glb.clouddn.com/mvncheck.PNG)
接下来下载你所需要的jar包，并获取所在的jar包路径
![](http://7xpp0b.com1.z0.glb.clouddn.com/ojdbc6.PNG)
然后在cmd中执行下面的命令在本地Maven仓库中安装这个jar包
```
mvn install:install-file 
-Dfile=D:\\test\\ojdbc6.jar
-DgroupId=com.oracle
-DartifactId=ojdbc6
-Dversion=11.1.0.7.0 
-Dpackaging=jar
```
如果出现`BUILD SUCCESS`则说明安装成功
![](http://7xpp0b.com1.z0.glb.clouddn.com/mvninstall.PNG)
那么你可以直接在Maven上pom.xml文件中直接添加引用
```
<dependency>
	<gropuId>com.oracle </gropuId>
	<artifactId>ojdbc6</artifactId>
	<version>11.1.0.7.0</version>
</dependency>
```