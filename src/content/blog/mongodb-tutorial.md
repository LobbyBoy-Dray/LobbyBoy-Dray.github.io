---
title: MongoDB Cheat Sheet
author: Jingwei Gao
pubDatetime: 2017-09-09T19:48:28+08
postSlug: mongodb-tutorial
featured: false
draft: false
tags:
  - database
description: A quick reference manual for MongoDB commands.
---

## Table of contents

## 一、MongoDB的相关概念

|   SQL概念   | MongoDB概念 |               说明                |
| :---------: | :---------: | :-------------------------------: |
|  database   |  database   |              数据库               |
|    table    | collection  |             表 / 集合             |
|     row     |  document   |             行 / 文档             |
|   column    |    field    |              列 / 域              |
|    index    |    index    |               索引                |
| primary key | primary key | 主键，MongoDB自动将\_id设置为主键 |

### 1、数据库

一个mongodb中可以建立**多个数据库**，每个都可以有自己的**集合**和**权限**。

**show dbs**可以显示所有的数据库。

**db**可以查看当前的数据库。

**use 数据库名**可以连接到一个指定数据库。

一些保留的数据库名：

- admin
- local
- config

### 2、集合

**collection，集合**

- 一组文档，类似于数据表；
- 集合存在于数据库中，没有固定结构；
- 在mongodb中，每个集合中的文档都有一个唯一的**\_id字段**，作用是作为**主键**；

### 3、文档

文档是一组键值对**key / value**（BSON）——**document**；

每个文档**不需要设置相同的字段，且相同的字段下不需要相同的数据类型**——和关系型数据库很大的区别；

- 文档的键值对是有序的，顺序不同，文档亦不同；
- 文档的值可以使用**字符串**、**整数**、**数组**以及**文档**等类型；
- 文档的键常是用**双引号标识的字符串**；
- 文档区分大小写以及值类型。

## 二、数据库的CRUD操作

CRUD——create，read，update，delete，增查改删

### 0、数据库的创建和删除

创建（切换）数据库——`use 数据库名`，若数据库不存在则创建，否则切换到指定数据库；**若数据库中没有数据，则show dbs不会显示**。

删除数据库——`db.dropDatabase()`，删除当前数据库，可以用db命令查看当前数据库名称。

### 1、增加（insert）

`db.集合名.insert(文档)`
如果集合不存在，则创建该集合并插入。

```sql
db.users.insert(
	{
		name:"sue",
		age:26,
		status:"pending"
	}
)
```

字段名可以不加双引号了。

如果添加的document没有\_id字段，那么mongodb会自动加上该字段。

**show collections**，查看当前数据库中的所有集合。

另外，也可以先将文档赋给一个变量，然后用变量名插入集合：

```sql
document = {name:"tanghao", age:9, sex:"man" }
-- 会返回这个文档
db.集合名.insert(document)
-- 将这个文档插入集合
```

**3.2版本更新**

- insertOne：插入一条文档数据；
- **insertMany**：插入多条文档数据，**数组**形式参数。

### 2、查询

1. 导入
   - `mongoimport --db test --collection restaurants --drop --file ~/downloads/primer-dataset.json`

#### 2）显示全部数据

`db.集合名.find()`

- 无参数——显示整个集合；
- 参数也是json形式——按条件查找；
- 自动生成\_id主键；
- findOne方法只返回一个文档。

`.pretty()`

- 好看的形式显示

#### 3）查询特定数据（等于条件）

`{ <field1>: <value1>, <field2>: <value2>, ... }`

- 一般对于最外层的字段，用上述形式——Top Level Field；
- 对于嵌套在内部的字段（Embedded Document），用点操作符：`db.restaurants.find( { "address.zipcode": "10075" } )`，意思是查找“address中zipcode是10075”的数据。

#### 4）查询特定数据（含操作符）

`{ <field1>: { <operator1>: <value1> } }`

- **大于**：$gt
- **小于**：$lt
- **大于等于**：$gte
- **小于等于**：$lte
- **不等于**：$ne
- **等于**：$eq

#### 5）多条件组合

**AND**：用逗号就行，并列；
**OR**：`$or:[{条件一},{条件二}]`
and和or可以联合用

#### 6）排序

排序：`.sort(条件)`

- `{"username":1}`
- 1：正序
- -1：倒序

多个条件：`.sort({'userbane':1, 'age':1})`

### 3、修改（Update）

```sql
db.collection.update(
   <query>,
   <update>,
   {
     upsert: <boolean>,
     multi: <boolean>,
     writeConcern: <document>
   }
)
```

- query：查询条件；
- update：更新操作；
- multi：可选，默认false，只更新找到的第一条记录；
- writeConcern：可选，抛出异常的级别。

#### 1）常规修改

`db.集合名.update(条件，内容)`

- 内容：`{$set:{json数据}}`

例如：

```sql
db.restaurants.update(
    { "name" : "Juni" },
    {
      $set: { "cuisine": "American (New)" },
    }
)
```

将restaurants集合中的**第一个**name是Juni的文档的cuisine字段改成American (New)。

#### 2）嵌套修改

还是用**点操作符**

#### 3）全部修改

使用**multi**

例：

```sql
db.restaurants.update(
  { "address.zipcode": "10016", cuisine: "Other" },
  {
    $set: { cuisine: "Category To Be Determined" },
  },
  { multi: true}
)
```

上述语句，将restaurants集合中**全部**address.zipcode字段是10016、cuisine字段是Other的文档的cuisine字段改成Category To Be Determined。

#### 4）直接替换

不用set，第二个参数直接附上一个document

**After the update, the document only contains the field or fields in the replacement document.**

### 4、删除

```sql
db.collection.remove(
   <query>,
   {
     justOne: <boolean>,
     writeConcern: <document>
   }
)
```

- query：查询条件；
- justOne：如果设为true或1，则只删除一个文档；
- writeConcern：可选，抛出异常的级别。

#### 1）删除全部满足条件的文档

`db.集合名.remove(条件)`

- 先find，再remove，安全；
- 全部删除，all documents；

#### 2）删除单条满足条件的文档

`db.集合名.remove(条件，{justOne:true})`
`db.集合名.remove(条件，1)`

#### 3）删除全部文档

`db.集合名.remove({})`

#### 4）删除整个集合

`db.集合名.drop()`

drop成功返回true，否则false

### 5、聚合

作用：aggregate主要用于处理数据，诸如计算平均值、求和等，并返回计算后的数据结果。

`db.COLLECTION_NAME.aggregate(AGGREGATE_OPERATION)`

举例：

```sql
db.mycol.aggregate([{$group:{_id:"$字段1"}, num_tutorial:{$sum:1}}])
```

## 三、索引

二叉树，B-tree

提高查询效率——没有索引，会把数据整个遍历一遍。
**Without indexes, MongoDB must perform a collection scan, i.e. scan every document in a collection, to select those documents that match the query statement.**

**查看索引**：`db.集合名.getIndexes()`

**创建索引**：`db.集合名.createIndex({字段名:1或-1})`或者`db.集合名.ensureIndex({字段名:1或-1})`

**创建复合索引**：逗号隔开
