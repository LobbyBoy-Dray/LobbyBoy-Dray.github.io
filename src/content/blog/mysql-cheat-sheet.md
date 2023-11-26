---
title: MySQL Cheat Sheet
author: Jingwei Gao
pubDatetime: 2018-02-07T21:28:11+08
postSlug: mysql-cheat-sheet
featured: false
draft: true
tags:
  - database
description: A quick reference manual for MySQL commands.
---

> A quick reference manual for MySQL commands.

## Table of contents

## 一、使用前

- 使用**管理员**身份启动命令行
- `net start mysql57`——启动mysql服务
- （`net stop mysql57`——关闭mysql服务）
- `mysql -uroot -p`，输入密码`root`
- 打开数据库

## 二、数据库操作：database

### 1、查看与进入数据库

显示所有数据库：
`show databases;`

查看指定数据库的定义：
`show create database db_name;`

打开指定数据库：
`use db_name;`

显示当前打开的数据库名称：
`select database();`

### 2、创建与删除数据库

创建数据库：
`create database if not exists db_name default character set = charset_name;`

删除数据库：
`drop database if exists db_name;`

修改数据库编码方式：
`alter database db_name default character set = charset_name;`

## 三、数据表操作：table

### 1、数据类型

#### 1）整数

**tiny一百多，small三万多，medium三千万多，一般这三个就够了。**

- `tinyint`：-128到127,或0到255，1字节；
- `smallint`：正负15次方，2字节——32768，三万多；
- `mediumint`：正负25次方，3字节——33554432，三千万多；
- `int`：正负31次方。4字节；
- `bigint`：正负63次方，8字节；
- `bool`，`boolean`：等价于tinyint(1)，0为false，其余为true；

#### 2）浮点

- `float[M, D]`：M是总位数，D是小数位数，4字节；
- `double[M, D]`：M是总位数，D是小数位数，8字节；
- `decimal[M, D]`：与double一样，但是内部以字符串形式存储数值，M+2字节，精度高不会进行四舍五入；

#### 3）字符串

**一般用varchar，text的话，tinytext和text足够。**

- `char(M)`：定长字符串，M是字符数，0到255；
- `varchar(M)`：变长字符串，65535个字符；
- `tinytext`：255；
- `text`：六万五；
- `mediumtext`
- `longtext`
- `enum('valiue1', 'value2', ...)`：枚举类型，比如性别要么是男，要么是女，要么是保密，只能选择一个值；
- `set('valiue1', 'value2', ...)`：集合类型，可以选择多个值；

#### 4）日期时间类型

- `time`：-828:59:59 ~ 838:59:59；
- `date`：1000-01-01 ~ 9999-12-31；
- `datetime`：1000-01-01 00:00:00 ~ 9999-12-31 23:59:59；
- `timestamp`：1970-01-01 00:00:01 utc ~ 2038-01-19 03:14:07；
- `year`：1901 ~ 2155；

### 2、查看Table

查看数据库下的数据表：
`show tables；`

查看表结构

- `desc tbl_name`
- `describe tbl_name`
- `show columns from tbl_name`

### 3、创建Table

`create table [if not exists] tbl_name(字段名称 字段类型 [完整性约束条件]) engine=引擎名称 charset=’编码方式‘`

**举例**

```sql
--注意：当需要输入中文的时候，需要临时转换客户端的编码方式
--SET NAMES GBK;
--字段注释 通过 COMMENT 注释内容

CREATE TABLE IF NOT EXISTS user(
	id SMALLINT,
	username VARCHAR(20),
	age TINYINT,
	sex ENUM('男','女','保密'),
	email VARCHAR(50),
	addr VARCHAR(200),
	birth YEAR,
	salary FLOAT(8,2),
	tel INT,
	married TINYINT(1) COMMENT '0代表为结婚，非0代表结婚'
) ENGINE = INNODB CHARSET=UTF8;
```

### 4、完整性约束条件

**只要记住这个公式`create table if not exists tb_name(字符名称 字段类型 [unsigned | zerofill] [not null] [default 默认值] [[primary] key | unique [key]] [auto_increment]) engine = innodb charset = utf8 auto_increment = 100;`**

#### 1）主键：primary key

- 主键唯一，意思是不能重复；
- 但可以设置多字段主键——多字段主键还是一个主键，复合主键；
- 多字段主键其中一个可以重复；
- 可省略primary，直接写key；

示例：

```sql
create table if not exists user1(
	id int primary key,
	username varchar(20)
);
```

多字段主键示例：

```sql
create table if not exists user1(
	id int,
	username varchar(20),
	card char(10),
	primary key(id, card)
);
```

#### 2）自增长：auto_increment

- 默认从1开始增长；
- 每次增加1；
- 一个表中只能有一个自增长字段，且一定是主键；
- 只对整数有效果，字符串不行；
- 建表时可以指定自增长值，例如从100开始自增长`auto_increment = 100`；

示例：

```sql
create table if not exists user2(
	id smallint key auto_increment,
	username varchar(20)
);
```

关于自增长字段：

- 若insert的时候指定了，则按照指定的来；
- 未指定，则最大值加1；
- 用null或default指定，不算指定；
- 可以使用`alter table tb_name auto_increment = xxx`修改自增长值。

#### 3）非空：not null

- 默认可以为空；
- 填写数据的时候不可以不填；
- 填写数据的时候不可以填null；

示例：

```sql
create table if not exists user3(
	id int unsigned key auto_increment,
	username varchar(20) not null,
	password char(32) not null,
	age tinyint unsigned
);
```

#### 4）默认值：default

- 如果不赋值怎么给值

```sql
create table if not exists user4(
	id int unsigned key auto_increment,
	username varchar(20) not null,
	password char(32) not null,
	age tinyint unsigned default 18,
	addr varchar(20) not null default 'beijing',
	sex enum('man', 'woman') not null default 'man'
);
```

#### 5）唯一：unique key

- 一个表中只能有一个主键，但可以有多个唯一；
- unique可以null；
- 被标志成唯一，则不能重复，除了null；
- key可以省略（区别于主键省略primary）；

示例：

```sql
create table if not exists user5(
	id tinyint unsigned key auto_increment,
	username varchar(20) not null unique,
	card char(18) unique
);
```

### 5、修改Table（结构）

`alter table tb_name ...`

#### 1）修改表名

`alter table tb_name rename [to | as ] new_name`

#### 2）添加字段

`alter table tb_name add 字段名称 字段类型 [完整性约束] [first | after 哪个字段]`

默认添加到末尾。

#### 3）删除字段

`alter table tb_name drop 字段名;`

#### 4）修改字段（总）

`alter table tb_name modify 字段名称 字段类型 [完整性约束] [first | after 哪个字段];`

#### 5）修改字段（名称）

`alter table tb_name change 旧字段名称 新字段名称 字段类型 [完整性约束] [first | after 字段名称];`

#### 6）修改字段（添加删除默认值）

**添加**

`alter table tb_name alter 字段名称 set default 默认值;`

**删除**

`alter table tb_name alter 字符名称 drop default;`

#### 7）修改字段（添加删除主键）

**添加**
`alter table tbl_name add [constraint [symbol] primary key[index_type]](字段名称，。。。)`

可以添加复合主键；

**删除**
`alter table tbl_name drop primary key`

#### 8）修改字段（添加删除唯一）

**添加**

`alter table tbl_name add [constraint [symbol]] unique [index|key] [索引名称]（字段名称，。。。）`

可以添加复合索引；
默认索引为字段名称；

**删除**

用索引来删除；

`alter table tbl_name drop {index|key} index_name`

#### 9）其他

修改表的存储引擎

`alter table tlb_name engine = 存储引擎名称`

设置自增长的值

`alter table tbl_name auto_increment = 值`

### 6、删除Table

`drop table if exists tb_name [, tb_name];`

## 四、数据表数据添加、更新与删除

### 1、插入数据

不指定具体的字段名：
`insert [into] tb_name values | value (值...);`

列出指定字段：
`insert [into] tb_name(字段名称1...) values | value (值1...);`

同时插入多条记录：
`insert [into] tb_name values(值1...), (值1...);`

---

- 通过set形式插入记录：insert [into] tbl_name set (字段名称) = (值)，(字段名称) = (值)...
- 将查询结构插入到表中：insert [into] tbl_name [(字段名称，...)] select 字段名称 from tbl_name [where 条件]

### 2、更新数据

`update tb_name set 字段名称 = 值, ... [where 条件] [order by 字段名称] [limit 限制条数];`

- 如果不加后面的条件，表中相应所有数据都会被更新；
- 多个更新，逗号分隔；

### 3、删除数据

`delete from tbl_name [where 条件] [order by 字段名称] [limit 限制条数]；`

并没有重置auto_increment

## 五、pymysql

```python
import pymysql

# 打开指定数据库连接
db = pymysql.connect('localhost', '用户名', '密码', '数据库名'

# 创建游标对象
cursor = db.cursor()

# 创建数据表
sql = ...
cursor.execute(sql)

# 向数据表中插入
sql = "INSERT INTO EMPLOYEE(FIRST_NAME, \
       LAST_NAME, AGE, SEX, INCOME) \
       VALUES ('%s', '%s', '%d', '%c', '%d' )" % \
       ('Mac', 'Mohan', 20, 'M', 2000)
try:
	cursor.execute(sql)
	db.commit()
except:
	db.rollback()

# 更新数据表
sql = ...
try:
	cursor.execute(sql)
	db.commit()
except:
	db.rollback()

# 删除数据
sql = ...
try:
	cursor.execute(sql)
	db.commit()
except:
	db.rollback()

# 查询
# fetchone(): 该方法获取下一个查询结果集。结果集是一个对象
# fetchall(): 接收全部的返回结果行.
sql = ...
try:
   cursor.execute(sql)
   results = cursor.fetchall()
except:
   print ("Error: unable to fetch data")
```
