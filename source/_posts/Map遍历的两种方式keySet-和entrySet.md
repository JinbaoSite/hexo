---
title: Map遍历的两种方式keySet()和entrySet()
date: 2017-11-15 20:32:04
categories: [Java]
---
Map集合中虽然提供了get()获取元素的方法，但是只能取出其对应的值，而不能全部取出，所以在Map集合中提供了两种遍历方式：keySet()和entrySet()。keySet()和entrySet()在Map元素数量小于$10^5$时，查询速度区别不大，但是当大于$10^5$时，entrySet()的速度明显快于keySet()，所以尽量使用entrySet()方式来进行Map集合遍历

#### 1 KeySet()的用法

```
Map<String,String> map = new HashMap<String,String>();
map.put("a","b");
//通过keySet()方法获取Map集合的所有键Set集合
Set<String> keySet = map.keySet();
Iterator<String> it =keySet.iterator();	//迭代器
while (it.hasNext()){
	String key = it.next();
	String value = map.get(key);
}
```

#### 2 entrySet()的用法

```
Map<String,String> map = new HashMap<String,String>();
map.put("a","b");
//通过entrySet()方法将map集合中的映射关系取出，得到的映射关系是Map.Entry类型
Set<Map.Entry<String,String>> entrySet = map.entrySet();	
Iterator<Map.Entry<String,String>> it = entrySet.iterator();	//迭代器
while (it.hasNext()){
	Entry<String,String> me = it.next()
	String key = me.getKey();
	String value = me.getValue();
}
```