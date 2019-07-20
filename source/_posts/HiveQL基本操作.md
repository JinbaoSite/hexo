---
title: HiveQL基本操作
date: 2018-05-07 21:24:01
categories: [大数据]
---

HiveQL是Hive查询语言，它的语法与MySQL最接近，但是Hive不支持行级别插入操作、更新操作和删除操作，同样也不支持事务

## 1 Hive数据库

Hive中数据库的概念本质上仅仅是表的一个目录或者命名空间，使用数据库可以表明表命名冲突。
如果用户没有显式指定数据库，那么将会使用默认的数据库default。

### 1.1 创建数据库

创建一个数据库与SQL语言一样
```
CREATE DATABASE test;
```
![创建数据库](http://7xwian.com1.z0.glb.clouddn.com/create.png)
有时候为了避免数据库已经存在，而又创建一个同名数据库引发错误，我们可以使用下面的语句
```
CREATE DATABASE IF NOT EXISTS test;
```
通常我们创建一个新的数据库，Hadoop会在`/user/hive/warehouse/`创建一个`/user/hive/warehouse/test.db`的目录，接下来我们存储的数据都会存放在这个目录下。
![数据库存储位置](http://7xwian.com1.z0.glb.clouddn.com/createsave.png)
当然我们也可以自己来指定这个目录，这个目录位于Hadoop上，会自动创建。
```
CREATE DATABASE testA
LOCATION '/testA';
```
![另设置数据库存储位置](http://7xwian.com1.z0.glb.clouddn.com/testA.png)
我们还可以在创建数据库的时候为这个数据库增加要给描述信息
```
CREATE DATABASE testB
COMMENT 'test for database';
```
除了描述信息，还可以增加相关的键-值对属性信息
```
CREATE DATABASE testC
WITH DBPROPERTIES ('creator' = 'JinbaoSite', 'data' = '2018-05-07');
```
![增加元信息](http://7xwian.com1.z0.glb.clouddn.com/addother.png)

### 1.2 查看所有数据库

查看数据库的命令为
```
SHOW DATABASES;
```
如果数据库太多，使用上面的语句会显示全部的数据库，我们可以正则表达式来筛选出需要的数据库
```
SHOW DATABASES LIKE 't.*';
```
![查看所有数据库](http://7xwian.com1.z0.glb.clouddn.com/show1.png)

### 1.3 查看数据库具体信息

我们可以使用下面语句来查看数据库的具体信息
```
DESCRIBE DATABASE testA;
```
还可以增加`EXTENDED`来查看更加具体的信息，比如我们之前添加的元信息。
```
DESCRIBE DATABASE EXTENDED testB;
DESCRIBE DATABASE EXTENDED testC;
```
![查看数据库具体信息](http://7xwian.com1.z0.glb.clouddn.com/de3.png)

### 1.4 设置当前工作数据库

USE命令用于将数据设置为用户当前的工作数据库
```
USE test;
```
不过我们使用这个USE并没有什么感觉，因为它并不会显示在哪个数据库下，也无法查看当前是哪个数据库。但是我们可以反复使用USE，这并不会进行嵌套。
我们也可以通过设置
```
set hive.cli.print.current.db=true;
```
显式展示当前的数据库。
![设置当前工作数据库](http://7xwian.com1.z0.glb.clouddn.com/use.png)

### 1.5 删除数据库

用户可以删除数据库
```
DROP DATABASE testB;
DROP DATABASE IF EXISTS testC;
```
使用`IF EXISTS`可以避免因数据库不存在而报出警告信息。
![删除数据库](http://7xwian.com1.z0.glb.clouddn.com/droptable.png)
删除testB和testC都会删除其在Hadoop上的存储路径，但是删除testA并不会
![删除后Hadoop路径](http://7xwian.com1.z0.glb.clouddn.com/l22.png)
通常情况下Hive是不允许用户删除一个包含表的数据库，用户需要先删除数据库中的表，然后再删除数据库，但是也可以在后面增加`CASCADE`使Hive自行先删除数据库中的表
```
DROP DATABASE IF EXISTS testD CASCADE;
```
![删除含表的数据库](http://7xwian.com1.z0.glb.clouddn.com/4.png)

### 1.6 数据库增加键-值对属性信息

数据库的元数据一般都是不可更改的，但是我们可以增加相关的键-值对属性信息
```
ALTER DATABASE test SET DBPROPERTIES('edited-by' = 'dongjinbao')
```
![增加元数据](http://7xwian.com1.z0.glb.clouddn.com/935.png)

## 2 Hive表

在Hive数据类型和文件格式中已经提及了如何创建表，我们创建表可以跟数据库一样增加相关信息
```
CREATE TABLE IF NOT EXISTS employees (
	name	STRING COMMENT 'Employees name',
	salary	FlOAT COMMENT 'Employee salary',
	subordiantes	ARRAY<STRING> COMMENT 'Names of subordinates',
	deductions	MAP<STRING,FLOAT> COMMENT 'Keys are deductions names, values are percentages',
	address 	STRUCT<country:STRING, city:STRING, state:STRING, zip:INT> COMMENT 'Home address'
)
COMMENT 'Description of the table'
TBLPROPERTIES ('creator' = 'JinbaoSite')
LOCATION '/home/dongjinbao/test/employees';
```
这里我们为每个字段都添加了信息，表描述信息，以及存储位置。
如果你使用了`IF NOT EXISTS`，如果表已经存在了，Hive会忽略掉后面的执行语句，而且不会有任何提示。
TBLPROPERTIES一般用作表示数据库的必要元数据信息，一般会自动增加两个表属性，一个是last_modified_by，保存最后修改表的用户名，另一个是last_modified_time，保存最后一次修改的时间。

我们也可以从一个已经存在的表里copy创建一个新表
```
CREATE TABLE IF NOT EXISTS employees2
LIKE employees;
```
在数据下查看所有的表为
```
SHOW TABLES;
```
我们也可以指定在哪个数据库下查看所有的表
```
SHOW TABLES IN test;
```
如果表过多，也可以使用正则表达式
```
SHOW TABLES 't.*';
```
不过，不支持上面两个语句结合使用！

查看表的相关信息，跟数据库一样，使用同样的关键字
```
DESCRIBE EXTENDED employees;
```
如果要非常详细的信息，可以使用FORMATTED替换EXTENDED
```
DESCRIBE FORMATTED employees;
```
我们也可以查看某个表中某个字段的描述
```
DESCRIBE FORMATTED employees.salary;
```

通常我们会为表创建分区，Hive创建分区的方式非常简单
```
CREATE TABLE employees (
	name	STRING,
	salary	FlOAT,
	subordiantes	ARRAY<STRING>,
	deductions	MAP<STRING,FLOAT>,
	address 	STRUCT<country:STRING, city:STRING, state:STRING, zip:INT>
)
PARTITIONED BY (country STRING,state STRING);
```
这里按照country和state进行了分区。
分区改变了Hive对数据存储的组织方式，原先默认的表存储路径为`/user/hive/warehouse/test.db/employees`，我们创建分区后，Hive会根据分区的值创建相应的子目录
```
/user/hive/warehouse/test.db/employees/country=A/state=AA
/user/hive/warehouse/test.db/employees/country=A/state=BB
/user/hive/warehouse/test.db/employees/country=B/state=CC
/user/hive/warehouse/test.db/employees/country=B/state=DD
```
我们可以在查询语句中增加WHERE条件来使用分区
```
SELECT * FROM employees
WHERE country = 'A' and state = 'BB';
```
如果你对一个表进行了分区，那么你对它进行查询而不使用分区信息的话，执行这个查询可能会触发一个巨大的MapReduce任务。你可以将Hive设置为`strict`模式
```
set hive.mapred.mode=strict;
```
这样对有分区的表进行查询而WHERE子句没有加分区过滤，将会禁止提交这个查询任务。
我们可以通过`SHOW PARTITIONS`查看表中存在的所有分区。
```
SHOW PARTITIONS employees;
```
分区是存在多个层级的，我们也可以只查看某一层级下的所有分区，
```
SHOW PARTITIONS employees PARTITION(country='A')
SHOW PARTITIONS employees PARTITION(country='A',state='AA')
```
如果你不知道某个表的分区字段是哪些，你可以使用`DESCRIBE EXTENDED table`来显示出表的所有信息，可以查看分区字段
```
DESCRIBE EXTENDED employees;
```

Hive删除表会将表的元数据和表内的数据全部删除
```
DROP TABLE IF EXISTS employees;
```
如果你使用LOCATION将表存储在指定的地方，那么只会删除表的元数据，并不会删除表内的数据。

对表的修改通过`ALTER TABLE`语句来进行修改，不过这种修改会修改元数据，并不会修改数据本身。
例如
1、对表进行重命名
```
ALTER TABLE employees RENAME TO A;
```
2、增加分区
```
ALTER TABLE employees ADD IF NOT EXISTS
PARTITION (country='C',state='EE') LOACATION '/home/dongjinbao/test/employees/C/EE'
PARTITION (country='C',state='FF') LOACATION '/home/dongjinbao/test/employees/C/FF';
```
3、修改分区路径
```
ALTER TABLE employees PARTITON(country='C',state='EE')
SET LOCATION '/home/dongjinbao/test/employees/C/EE';
```
这个修改分区路径不会转移数据，也不会删除数据。
4、删除分区
```
ALTER TABLE employees DROP IF EXISTS PARTITION(country='C',state='EE');
```

我们也可以对表的某个字段进行重命名，并修改其位置、类型或者注释
```
ALTER TABLE employee
CHANGE COLUMN salary S INT
COMMENT 'Change name salary to S'
AFTER name;
```