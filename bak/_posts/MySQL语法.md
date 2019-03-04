title: MySQL语法
date: 2019-02-13 18:24:05
tags: [mysql]
categories: [开发]
---
#### 相关术语
```
SQL语言：Structured Query Language：结构化查询语言
DDL：数据定义语言
DML：数据操作语言
DQL：数据查询语言
DCL：数据控制语言
```
<!--more-->
#### MySQL相关操作
- 语法规范
```
常用MySQL的关键字我们需要大写，库名、表名、字段名称等使用小写
SQL语句支持折行操作，拆分的时候不能把完整单词拆开
数据库名称、表名称、字段名称不要使用MySQL的保留字，如果必须要使用，需要用反引号``将其括起来
```
- 常见SQL语句
```
SELECT USER()	得到登陆的用户
SELECT VERSION()	得到MySQL的版本信息
SELECT NOW()	得到当前的日期时间
SELECT DATABASE()	得到当前打开的数据库
```

####数据库相关操作
- 创建数据库
```
CREATE {DATABASE|SCHEMA} db_name;
CREATE DATABASE [IF NOT EXISTS] db_name;
检测数据库名称是否存在，不存在则创建

在创建数据库的同时指定编码方式
CREATE DATABASE [IF NOT EXISTS] db_name [DEFAULT] CHARACTER SET [=] charset;
注意
-  数据库名称最好有意义
-  名称不要包含特殊字符或者是MySQL关键字
```
- 其他操作
```
查看当前服务器下全部数据库
SHOW DATABASES|SCHEMAS;
查看指定数据库的详细信息
SHOW CREATE DATABASE db_name;
修改指定数据库的编码方式
ALTER DATABASE db_name [DEFAULT] CHARACTER SET [=] charset;
打开指定数据库
USE db_name;
得到当前打开的数据库
SELECT DATABASE()|SCHEMA();
删除指定的数据库
DROP DATABASE db_name;
如果数据库存在则删除
DROP DATABASE [IF EXISTS] db_name;
```

#### 数据类型
- ![整数型](https://upload-images.jianshu.io/upload_images/2572206-3ef466b7f6778357.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- ![浮点型](https://upload-images.jianshu.io/upload_images/2572206-508f3260cc9546f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
- ![字符串类型](https://upload-images.jianshu.io/upload_images/2572206-50178ef974a74f2a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
CHAR效率高于VARCHAR,CHAR相当于拿空间换时间，VARCHAR拿时间换空间
CHAR默认存储数据的时候，后面会用空格填充到指定长度；而在检索的时候会去掉后面空格；VARCHAR在保存的时候不进行填充，尾部的空格会留下
TEXT列不能有默认值,检索的时候不存在大小写转换
```
- ![日期类型](https://upload-images.jianshu.io/upload_images/2572206-69d5099db7c4c69e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 数据库表操作
- 基础知识
```
是数据库最重要的组成部分之一，数据是保存在数据表中
数据表由行(row)和列(column)来组成
每个数据表中至少有一列，行可以有零行一行或者多行组成
表名要求唯一，不要包含特殊字符，最好含义明确
```
- 创建表
```
CREATE TABLE [IF NOT EXISTS] tbl_name(
    字段名称 字段类型 [完整性约束条件],
    字段名称 字段类型 [完整性约束条件],
	...
)ENGINE=存储引擎 CHARSET=编码方式;
```
- 约束
```
UNSIGNED	无符号，没有负数，从0开始
ZEROFILL	零填充，当数据的显示长度不够的时候可以使用前补0的效果填充至指定长度,字段会自动添加UNSIGNEDNOT NULL	非空约束，也就是插入值的时候这个字段必须要给值,值不能为空
DEFAULT	默认值，如果插入记录的时候没有给字段赋值，则使用默认值
PRIMARY KEY	主键，标识记录的唯一性，值不能重复，一个表只能有一个主键，自动禁止为空
AUTO_INCREMENT	自动增长，只能用于数值列，而且配合索引使用,默认起始值从1开始，每次增长1
UNIQUE KEY	唯一性，一个表中可以有多个字段是唯一索引，同样的值不能重复，但是NULL值除外
FOREIGN KEY	外键约束
```
- 查看数据库表的详细信息
```
SHOW CREATE TABLE tbl_name;

查看表结构
DESC tbl_name;
DESCRIBE tbl_name;
SHOW COLUMNS FROM tbl_name;
```
- 删除表
```
DROP TABLE [IF EXISTS] tbl_name;
```
- 表结构的相关操作
```
添加字段
ALTER TABLE tbl_name 
ADD 字段名称 字段属性 [完整性约束条件] [FIRST|AFTER 字段名称]

删除字段
ALTER TABLE tbl_name
DROP 字段名称

添加默认值
ALTER TABLE tbl_name
ALTER 字段名称 SET DEFAULT 默认值;

删除默认值
ALTER TABLE tbl_name
ALTER 字段名称 DROP DEFAULT

修改字段类型、字段属性
ALTER TABLE tbl_name
MODIFY 字段名称 字段类型 [字段属性] [FIRST | AFTER 字段名称]

修改字段名称、字段类型、字段属性
ALTER TABLE tbl_name
CHANGE 原字段名称 新字段名称 字段类型 字段属性 [FIRST | AFTER 字段名称]

添加主键
ALTER TABLE tbl_name
ADD PRIMARY KEY(字段名称)

删除主键
ALTER TABLE tbl_name
DROP PRIMARY KEY;

添加唯一
ALTER TABLE tbl_name
ADD UNIQUE KEY|INDEX [index_name] (字段名称)

删除唯一
ALTER TABLE tbl_name 
DROP index_name;

修改数据表名称
ALTER TABLE tbl_name 
RENAME [TO|AS] new_tbl_name
RENAME TABLE tbl_name TO new_tbl_name;

修改AUTO_INCREMENT的值
ALTER TABLE tbl_name AUTO_INCREMENT=值
```

#### MySQL数据操作
- 增
```
INSERT [INTO] tbl_name[(col_name,...)] {VALUE|VALUES}(VALUES...);

不指定字段名称
INSERT tbl_name VALUE(value...)
需要按照建表时的字段顺序给每一个字段赋值

列出指定字段
INSERT tbl_name(字段名称,...) VALUES(值,...)

INSERT ... SET的形式
INSERT tbl_name SET 字段名称=值,...;

INSERT ... SELECT
INSERT tbl_name[(字段名称...)] SELECT 字段名称,... FROM  tbl_name [WHERE 条件]

一次添加多条记录
INSERT tbl_name[(字段名称,...)] VALUES(值,...),
(值,....),
(值,...)
```
- 改
```
UPDATE tbl_name SET 字段名称=值,字段名称=值 [WHERE 条件]
如果不添加条件，整个表中的记录都会被更新
```
- 删
```
DELETE FROM tbl_name [WHERE 条件]
如果不添加条件，表中所有记录都会被删除

DELETE 清空数据表的时候不会重置AUTO_INCREMENT的值，可以通过ALTER 语句将其重置为1

彻底清空数据表
TRUNCATE [TABLE] tbl_name;
清除表中所有记录，会重置AUTO_INCREMENT的值
```
#### 查询
- 语法
```
SELECT select_expr,... FROM tbl_name
[WHERE 条件]
[GROUP BY {col_name|position} HAVING 二次筛选]
[ORDER BY {col_name|position|expr} [ASC|DESC]]
[LIMIT 限制结果集的显示条数] 
```
- 基础操作
```
查询表中所有记录
SELECT * FROM tbl_name;
*所有字段

指定字段的信息
SELECT 字段名称,... FROM tbl_name

库名.表名
SELECT 字段名称,... FROM db_name.tbl_name;

给字段起别名
SELECT 字段名称 [AS] 别名名称,... FROM db_name.tbl_name; 

给数据表起别名
SELECT 字段名称 ,... FROM tbl_name [AS] 别名;

表名.字段名的
SELECT tbl_name.col_name,... FROM tbl_name;
```
- where条件
```
  比较运算符
    > >= < <= != <> <=>
  
  <=> 和 =的区别?  <=>可以检测NULL值

  IS [NOT] NULL
  检测值是否为NULL或者NOT NULL

  指定范围  [NOT] BETWEEN ... AND

  指定集合  [NOT] IN(值,...)

  逻辑运算符
  AND  逻辑与
  OR  逻辑或

  匹配字符
	[NOT] LIKE
	%  任意长度的字符串
	_  任意一个字符
```
- GROUP BY 分组
```
把值相同放到一个组中，最终查询出的结果只会显示组中一条记录
分组配合GROUP_CONCAT()查看组中某个字段的详细信息

配合聚合函数使用
  COUNT()
    统计记录总数
    如果写的是COUNT(字段名称)，字段中的值为NULL，不统计进来
    写COUNT(*)会统计NULL值
  SUM()  求和
  MAX()  求最大值
  MIN()  求最小值
  AVG()  求平均值

配合WITH ROLLUP关键使用,会在记录末尾添加一条记录，是上面所有记录的总和
HAVING子句对分组结果进行二次筛选
```
- ORDER BY 排序
```
ORDER BY 字段名称 ASC|DESC
```
- LIMIT 限制结果集显示条数
```
LIMIT 值
显示结果集的前几条记录
LIMIT offset,row_count
从offset开始，显示几条记录,offset从0开始
```
- 多表查询
```
- 内连接：查询两个表中符合连接条件的记录
SELECT 字段名称,... FROM tbl_name1
INNER JOIN tbl_name2
ON 连接条件

- 左连接：先显示左表中的全部记录，再去右表中查询复合条件的记录，不符合的以NULL代替
SELECT 字段名称,... FROM tbl_name1
LEFT [OUTER] JOIN tbl_name2
ON 条件;

- 右连接：先显示右表中的全部记录，再去左表中查询复合条件的记录，不符合的以NULL代替
SELECT 字段名称,... FROM tbl_name1
 RIGHT [OUTER] JOIN tbl_name2
ON 条件;
```
- 子查询
```
SELECT 字段名称 FROM tbl_name WHERE col_name=(SELECT col_name FROM tbl_name)

- 内层语句查询的结果可以做为外层语句查询的条件
```
- 联合查询
```
SELECT 字段名称,... FROM tbl_name1 
UNION
SELECT 字段名称... FROM tbl_name2;

SELECT 字段名称,... FROM tbl_name1 
UNION ALL
SELECT 字段名称... FROM tbl_name2;

UNION ALL 是简单的合并，UNION会去掉表中重复记录
```