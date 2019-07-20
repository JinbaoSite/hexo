---
title: Python定期删除文件夹下的过期文件
date: 2018-02-11 00:22:55
categories: [Python]
---

有时候会产生很多一次性文件以及文件夹，访问一两次就不会再使用了，现需要定期进行清理过期的文件，并且删除空文件夹

```python
import os
import sys
import time

def delDir(dir,t=120):
	#获取文件夹下所有文件和文件夹
	files = os.listdir(dir)
	for file in files:
		filePath = dir + "/" + file
		#判断是否是文件
		if os.path.isfile(filePath):
			#最后一次修改的时间
			last = int(os.stat(filePath).st_mtime)
			#上一次访问的时间
			#last = int(os.stat(filePath).st_atime)
			#当前时间
			now = int(time.time())
			#删除过期文件
			if (now - last >= t):
				os.remove(filePath)
				print(filePath + " was removed!")
		elif os.path.isdir(filePath):
			#如果是文件夹，继续遍历删除
			delDir(filePath,t)
			#如果是空文件夹，删除空文件夹
			if not os.listdir(filePath):
				os.rmdir(filePath)
				print(filePath + " was removed!")

if __name__ == '__main__':
	#获取路径
	path = sys.argv[1]
	#获取过期时间
	t = int(sys.argv[2])
	#获取定期清理时间
	ts = int(sys.argv[3])
	while True:
		delDir(path,t)
		time.sleep(ts)
```

在命令行中执行
```
python delDir.py D:\\test
```
结果如下
![删除结果](http://o7zffg0ie.bkt.clouddn.com/delans.PNG)
