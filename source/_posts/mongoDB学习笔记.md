---
title: mongoDB学习笔记
date: 2017-03-04 15:49:10
tags: [mongodb,数据库,nosql]
categories: [学习,程序员,数据库,mongoDB]
---


**一、数据库的基本概念及操作**
|SQL术语/概念|  MongoDB术语/概念|   解释/说明|
|------|-------|--------|
|database|  database|   数据库|
|table| collection| 数据库表/集合|
|row|   document|   数据记录行/文档|
|column|    field|  数据字段/域|
|index| index|  索引|
|table| joins|      表连接,MongoDB不支持|
|primary key|   primary key|    主键,MongoDB自动将_id字段设置为主键|

<!--more-->
- **创建数据库**
```sql
> use mydb
```
- **查看当前连接的数据库**
```sql
> db

-> mydb
```
- **查看所有的数据库**
```sql
> show dbs

-> mydb
```

- **销毁数据库**
```sql
> use local
 switched to db local
> db.dropDatabase()
```

**二、集合(Collection)的基本操作**
- **创建集合**
```sql
> db.creationCollection('users')
```
- **查看创建的集合**
```sql
> show collections
```
- **删除集合**
```sql
> db.users.drop()
```
- **向集合中插入数据**

1.**==insert()==**
插入数据时，如果 users 集合没有创建会自动创建。
```sql
> use mydb
switched to db mydb
> db.users.insert([
... { name : "jam",
... email : "jam@qq.com"
... },
... { name : "tom",
... email : "tom@qq.com"
... }
... ])
```
2.**==使用 save()==**
插入数据时，如果 users 集合没有创建会自动创建。
```sql
> use mydb2
switched to db mydb2
> db.users.save([
... { name : "jam",
... email : "jam@qq.com"
... },
... { name : "tom",
... email : "tom@qq.com"
... }
... ])
```

- **更新数据**
**db.COLLECTION_NAME.update(SELECTION_CRITERIA,UPDATED_DATA)**
```sql
> use mydb2
switched to db mydb2
> db.mysb2.update({'name':'jam'},{'name':'bob'})
```

- **删除数据**
**db.COLLECTION_NAME.remove(DELECTION_CRITERIA)**
```sql
> use mydb2
switched to db mydb2
> db.mydb2.remove({'name':'tom'})
```
| 操作语句 | 作用 |
| -------  | -----|
|db.createCollection('COLLECTION_NAME')|创建集合|
|db.COLLECTION.drop()|删除集合|
|db.COLLECTION_NAME.insert(document)|插入文档|
|db.COLLECTION_NAME.update(SELECTION_CRITERIA,UPDATED_DATA)|更新文档|
|db.COLLECTION_NAME.save({_id:ObjectId(),NEW_DATA})|替换已存在的文档|
|db.COLLECTION_NAME.remove(DELECTION_CRITERIA)|删除文档|

**三、数据查询**
- **find()** 语句
```sql
> use mydb
switched to db mydb
> db.mydb.insert({'name':'mars','age':12,'gender':'man'})
```
1.不加参数时返回所有记录
```sql
> db.mydb.find()
{ "_id" : ObjectId("589dc5f3e86d38da8455b314"), "name" : "marsggbo" }
{ "_id" : ObjectId("589dcac5e86d38da8455b315"), "name" : "mars", "age" : 12, "gender" : "man" }
```
2.带参数
```sql
> use mydb
> db.mydb.find({'name':'mars'})
{ "_id" : ObjectId("589dcac5e86d38da8455b315"), "name" : "mars", "age" : 12, "gender" : "man" }
```

2.1 **条件操作符1**

>(>) 大于 - \$gt #greate
(<) 小于 - \$lt #low
(>=) 大于等于 - \$gte #equal
(<= ) 小于等于 - \$lte

2.2 **条件操作符2**
- **type**
```
$type
```
type的值：
>双精度型-1
字符串-2
对象-3
数组-4
二进制数据-5
对象ID-7
布尔类型-8
数据-9
空-10
正则表达式-11
JS代码-13
符号-14
有作用域的JS代码-15
32位整型数-16
时间戳-17
64位整型数-18
Min key-255
Max key-127

范例：

```sql
> use student
switched to db student
> db.student.find({"name":{$type:2}})
```
查找name是字符串的文档记录

- **limit和skip**
> limit:读取指定数量的数据记录 -limit
skip:读取时跳过指定数量的数据记录

首先查看student集合中年龄大于20的数据
```sql
> use student
switched to db student
> db.student.find({'age':{$gt:20}})
{ "_id" : ObjectId("589dcf6d6ac83b9ae69f63e6"), "name" : "z", "sex" : "m
an", "age" : 23 }
{ "_id" : ObjectId("589dcf8f6ac83b9ae69f63e8"), "name" : "z", "sex" : "m
an", "age" : 29 }
{ "_id" : ObjectId("589dd11ce3b6b824fd0620e9"), "age" : 22, "name" : "ab
", "sex" : "woman" }
```

限制只显示一个数据后，默认显示最前面的数据
(第一行命令不用重复编写，这里是因为markdown解析非得加上这句话才能高亮。。。)
```sql
> use student
switched to db student

> db.student.find({'age':{$gt:20}}).limit(1)
{ "_id" : ObjectId("589dcf6d6ac83b9ae69f63e6"), "name" : "z", "sex" : "m
an", "age" : 23 }
```

跳过一条数据，则可猜想显示结果为第二个数据
```sql
> use student
switched to db student

> db.student.find({'age':{$gt:20}}).limit(1).skip(1)
{ "_id" : ObjectId("589dcf8f6ac83b9ae69f63e8"), "name" : "z", "sex" : "m
an", "age" : 29 }
```

- **pretty()** 语句
作用是使查询输出的结果更美观
```sql
> use mydb
> db.mydb.find().pretty()
{ "_id" : ObjectId("589dc5f3e86d38da8455b314"), "name" : "marsggbo" }
{
    "_id" : ObjectId("589dcac5e86d38da8455b315"),
    "name" : "mars",
    "age" : 12,
    "gender" : "man"
}
```

- **sort()** 排序
> 与sqlite中的排序一样有升序和降序，其中升序用1表示，降序用-1表示 

示例
```sql
> use student
switched to db student
> db.student.find().sort({'age':1})
{ "_id" : ObjectId("589dcf866ac83b9ae69f63e7"), "name" : "z", "sex" : "f
eman", "age" : 19 }
{ "_id" : ObjectId("589dd11ce3b6b824fd0620e9"), "age" : 22, "name" : "ab
", "sex" : "woman" }
{ "_id" : ObjectId("589dcf6d6ac83b9ae69f63e6"), "name" : "z", "sex" : "m
an", "age" : 23 }
{ "_id" : ObjectId("589dcf8f6ac83b9ae69f63e8"), "name" : "z", "sex" : "m
an", "age" : 29 }
```

**四、数据索引**
**ensureIndex()**

>索引通常能够极大的提高查询的效率，如果没有索引，MongoDB在读取数据时必须扫描集合中的每个文件并选取那些符合查询条件的记录。这种扫描全集合的查询效率是非常低的，特别在处理大量的数据时，查询可以要花费几十秒甚至几分钟，无疑对网站的性能是非常致命的。

索引是特殊的数据结构，索引存储在一个易于遍历读取的数据集合中，索引是对数据库集合中一个文档或多个文档的值进行排序的一种结构。

语法：

    db.COLLECTION_NAME.ensureIndex({KEY:1|-1})
同样1代表升序，-1代表降序

范例：
```sql
> use student
switched to db student

> db.student.ensureIndex({"name":1})
```
ensureIndex()的可选参数：
|参数|    类型| 描述|
| -------  | -----|----|
background| Boolean|    建立索引要不要阻塞其他数据库操作，默认为false|
unique| Boolean|    建立的索引是否唯一，默认false|
name|   string| 索引的名称，若未指定，系统自动生成|
dropDups|Boolean    |建立唯一索引时，是否删除重复记录，默认flase|
|sparse |Boolean|对文档不存在的字段数据不启用索引，默认false|
|expireAfterSeconds|    integer|    设置集合的生存时间，单位为秒|
|v| index version|  索引的版本号|
|weights|   document|   索引权重值，范围为1到99999|
|default-language|  string| 默认为英语|
|language_override| string| 默认值为 language|
范例：
```
> db.shiyanlou.ensureIndex({"user_id":1,"name":1},{background:1})
```

总结：
可以使用find进行查询，sort用于排序，ensureIndex用于建立索引，aggregate用于聚合。