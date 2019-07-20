---
title: Win8使用VirtualBox安装CentOS 6.4教程
date: 2017-11-29 23:32:52
categories: [大数据]
---

## 1 安装工具

VirtualBox 5.1.30虚拟机【[下载](https://www.baidu.com/link?url=jFRD-NEnUMsEOz30yFZseExhrUQPHN70126rhx8W3_QOmC9IPW8q1ta4HoQ5maM2frsBrqV8C_60trqNb5_76IficaKdnf1Ou0LbxQb5_J3&wd=&eqid=fd987e310001c109000000055a1ec2d8)】
64位CentOs 6.4 【[下载](http://121.192.176.204/mirror.symnds.com/distributions/CentOS-vault/6.4/isos/x86_64/CentOS-6.4-x86_64-bin-DVD1.iso)】

## 2 安装VirtualBox

下载好后一直默认安装即可
![](http://p06qbh638.bkt.clouddn.com/20171129221055.jpg)
![](http://p06qbh638.bkt.clouddn.com/20171129221107.jpg)
![](http://p06qbh638.bkt.clouddn.com/20171129221128.jpg)
![](http://p06qbh638.bkt.clouddn.com/20171129221146.jpg)
![](http://p06qbh638.bkt.clouddn.com/20171129221156.jpg)
![](http://p06qbh638.bkt.clouddn.com/20171129221206.jpg)

## 3 使用VirtualBox安装CentOS

1、打开VirtualBox，点击“创建”按钮，创建一个虚拟机
![](http://p06qbh638.bkt.clouddn.com/%E5%88%9B%E5%BB%BA%E8%99%9A%E6%8B%9F%E7%A1%AC%E7%9B%9820171129221545.jpg)
2、命名虚拟机，选择操作系统和版本，这里是64位的
![](http://p06qbh638.bkt.clouddn.com/%E9%80%89%E6%8B%A9%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F20171129221416.jpg)
3、设置内存大小，方便虚拟机使用，一般推荐设置1G，如果你也有8G的内存，可以跟我一样设置2G或者更大的内存
![](http://p06qbh638.bkt.clouddn.com/%E8%AE%BE%E7%BD%AE%E5%86%85%E5%AD%98%E5%A4%A7%E5%B0%8F20171129221515.jpg)
4、创建虚拟硬盘
![](http://p06qbh638.bkt.clouddn.com/%E5%88%9B%E5%BB%BA%E8%99%9A%E6%8B%9F%E7%A1%AC%E7%9B%9820171129221545.jpg)
5、选择虚拟硬盘文件类型为VDI
![](http://p06qbh638.bkt.clouddn.com/%E5%88%9B%E5%BB%BA%E8%99%9A%E6%8B%9F%E7%A1%AC%E7%9B%98.jpg)
6、设置虚拟硬盘动态分配
![](http://p06qbh638.bkt.clouddn.com/%E8%99%9A%E6%8B%9F%E7%A1%AC%E7%9B%98%E5%8A%A8%E6%80%81%E5%88%86%E9%85%8D.jpg)
7、选择文件存储的位置和容量大小
![](http://p06qbh638.bkt.clouddn.com/%E6%96%87%E4%BB%B6%E4%BD%8D%E7%BD%AE%E5%92%8C%E5%A4%A7%E5%B0%8F.jpg)
8、最后create，进入下面的界面时，勿点击启动按钮，有可能导致进程中断，我们应该先设置存储，点击存储，打开存储界面
![](http://p06qbh638.bkt.clouddn.com/%E5%85%88%E8%AE%BE%E7%BD%AE%E5%AD%98%E5%82%A8.jpg)
9、在存储界面中，点击没有盘片，在分配光驱上选择一个虚拟光盘，选中你下载好的Centos的IOS文件，点击OK
![](http://p06qbh638.bkt.clouddn.com/%E5%AD%98%E5%82%A8%E8%AE%BE%E7%BD%AE.jpg)
![](http://p06qbh638.bkt.clouddn.com/%E9%80%89%E4%B8%ADIOS%E6%96%87%E4%BB%B6.jpg)
10、选择刚创建的虚拟机CentOS，点击启动
![](http://p06qbh638.bkt.clouddn.com/%E5%90%AF%E5%8A%A8Centos.jpg)
11、进入CentOS安装界面后，选择第二个安装
![](http://p06qbh638.bkt.clouddn.com/%E5%AE%89%E8%A3%852.jpg)
12、在Disc Found中选择Skip
![](http://p06qbh638.bkt.clouddn.com/%E9%80%89%E6%8B%A9Skip.jpg)
13、进入Centos界面，点击Next
![](http://p06qbh638.bkt.clouddn.com/%E7%82%B9%E5%87%BBNext.jpg)
14、语言选择English，选择中文可能会报错
![](http://p06qbh638.bkt.clouddn.com/%E9%80%89%E6%8B%A9%E8%AF%AD%E8%A8%80.jpg)
15、键盘选择U.S.English
![](http://p06qbh638.bkt.clouddn.com/%E9%80%89%E6%8B%A9%E9%94%AE%E7%9B%98.jpg)
16、选择Basic Storage Devices
![](http://p06qbh638.bkt.clouddn.com/%E9%80%89%E6%8B%A9Basic.jpg)
17、出现存储警告时，选择忽略
![](http://p06qbh638.bkt.clouddn.com/%E9%80%89%E6%8B%A9Yes.jpg)
18、设置HostName
![](http://p06qbh638.bkt.clouddn.com/%E8%AE%BE%E7%BD%AEhostname.jpg)
19、设置时区
![](http://p06qbh638.bkt.clouddn.com/%E8%AE%BE%E7%BD%AE%E6%97%B6%E5%8C%BA.jpg)
20、设置密码，密码长度必须大于6位
![](http://p06qbh638.bkt.clouddn.com/%E8%AE%BE%E7%BD%AE%E5%AF%86%E7%A0%81.jpg)
21、替换原有Linux系统
![](http://p06qbh638.bkt.clouddn.com/%E6%9B%BF%E6%8D%A2%E5%8E%9F%E6%9C%89%E7%B3%BB%E7%BB%9F.jpg)
22、选择Write changes to disk
![](http://p06qbh638.bkt.clouddn.com/writechanges.jpg)
23、选择minimal
![](http://p06qbh638.bkt.clouddn.com/%E9%80%89%E6%8B%A9minimal.jpg)
24、安装完成后，重启，输入root和密码即可进入
![](http://p06qbh638.bkt.clouddn.com/%E9%87%8D%E5%90%AFroot.jpg)
![](http://p06qbh638.bkt.clouddn.com/%E7%99%BB%E5%BD%95.jpg)

