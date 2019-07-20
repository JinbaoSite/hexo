---
title: Hive数据类型和文件格式
date: 2018-05-07 20:31:37
categories: [大数据]
---

Hive 支持关系型数据库中的大多数基本数据类型，同样也支持关系型数据库中很少出现的3中集合数据类型。

## 1 基本数据类型

| 数据类型 | 描述 |  
|:-------------:| :-----:| 
| TINYINT | 1byte有符合整数 |
| SMALINT | 2byte有符合整数 |
| INT | 4byte有符合整数 |
| BIGINT | 8byte有符合整数 |
| BOOLEAN | 布尔类型，true或false |
| FLOAT | 单精度浮点数 |
| DOUBLE | 双精度浮点数 |
| STRING | 字符序列 |
| TIMESTAMP | 整数，浮点数或者字符串 |
| BINARY | 字节数组 |

这些数据类型其实都是对Java中的接口的实现，因此这些类型的具体行为细节和Java中对应的类型是完全一致的。
数据类型TIMESTAMP的值可以是整数、浮点数或者字符串，通常表示的是UTC时间，Hive本身提供了不同时区互相转换的内置函数，也就是`to_utc_timestamp`函数和`from_utc_timestamp`函数

## 2 集合数据类型

| 数据类型 | 描述 |  
|:-------------:| :-----| 
| STRUCT | 类似于C中的struct |
| MAP | 一组键-值对元组集合，使用数组表示法可以访问元素 |
| ARRAY | 一组具有相同类型和名称的变量的集合，可以通过下标访问 |

STRUCT可以混合多种不同的数据类型，但是STRUCT中一旦声明好结构，那么其位置就不可以再改变。

## 3 示例

```
CREATE TABLE employees (
	name	STRING,
	salary	FlOAT,
	subordiantes	ARRAY<STRING>,
	deductions	MAP<STRING,FLOAT>,
	address 	STRUCT<country:STRING, city:STRING, state:STRING, zip:INT >
);
```
在这个TABLE中使用大多数的数据类型，方便理解如何使用Hive的数据类型。

## 4 文件格式

Hive中默认的记录和字段分割符

| 分割符 | 描述 |  
|:-------------:| :-----| 
| \n | 对于文件文件来说，每行都是一条记录，因此换行符可以分割记录 |
| ^A | 用于分割字段 |
| ^B | 用于分割ARRAY或STRUCT总的元素，或用于MAP中键-值对之间的分割 |
| ^C | 用于MAP中键和值之间的分割 |

Hive的数组其实最终是存储到文件上去的，上面的实例中的表中的一条数据在文件中的存储如下：
```
John Doe^A10000.0Mary Smith^BTodd Jones^AFederal Taxes^C0.2^BStateTaxes^C0.05^BInsurance^C0.01^A1 Michigan Ave^BCHICAGO^BIL^B60600
```
我们从上面其实无法看出结构，我们将其转换为JSON格式
```
{
	"name": "John Doe",
	"salary": 10000.0,
	"subordinates": ["Mary Smith", "Todd Jones"],
	"deductions": {
		"Federal Taxes": 0.2,
		"State Taxes": 0.05,
		"Insurance": 0.1
	},
	"address": {
		"street": "1 Michigan Ave.",
		"city": "Chicago",
		"state": "IL",
		"zip": 60600
	}
}
```
我们也可以对上面的分割符自己进行指定
```
CREATE TABLE employees (
	name	STRING,
	salary	FlOAT,
	subordiantes	ARRAY<STRING>,
	deductions	MAP<STRING,FLOAT>,
	address 	STRUCT<country:STRING, city:STRING, state:STRING, zip:INT >
)
ROW FOMAT DELIMITED
FIELDS TERMINATED BY '\001'
COLLECTION ITEMS TERMINATED BY '\002'
MAP KEYS TERMINATED BY '\003'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;
```
自己指定时，必须先加`ROW FOMAT DELIMITED`；
`FIELDS TERMINATED BY '\001'`这个子句表明Hive将使用`'\001'`字符作为列分割符；
`COLLECTION ITEMS TERMINATED BY '\002'`这个子句表明Hive将使用`'\002'`字符作为集合元素间分割符；
`MAP KEYS TERMINATED BY '\003'`这个子句表明Hive将使用`'\003'`字符作为MAP的键和值间的分割符；
`LINES TERMINATED BY '\n'`这个子句表明Hive将使用`'\n'`字符作为行和行间的分割符，其实行与行之间只能使用这个，目前仅支持`'\n'`；
`STORED AS TEXTFILE`这个子句表明Hive以文本的形式保存，事实上默认就是以文本的形式保存的。