---
title: MongoDB
date: 2017-03-02
tags:
- MongoDB
- NoSQL
- Database
---
<!-- TOC -->

- [MongoDB 基础](#mongodb-基础)
    - [MongoDB 概念](#mongodb-概念)
        - [document](#document)
        - [数据类型](#数据类型)
    - [安装](#安装)
    - [数据目录](#数据目录)
    - [运行MongoDB服务](#运行mongodb服务)
        - [MongoDB Shell](#mongodb-shell)
    - [基本命令](#基本命令)
        - [数据库](#数据库)
        - [集合](#集合)
        - [基本操作](#基本操作)
            - [插入(增)](#插入增)
            - [删除(删)](#删除删)
            - [更新(改)](#更新改)
            - [查询(查)](#查询查)
    - [关系符](#关系符)
    - [limit() skip()](#limit-skip)
    - [sort()](#sort)
    - [ensureIndex()](#ensureindex)
    - [aggregate()](#aggregate)
    - [备份&恢复](#备份恢复)

<!-- /TOC -->
# MongoDB 基础

@(Database)

[TOC]

## MongoDB 概念
|名词|说明|
|:-|:-|
|database|数据库,名字必须小写|
|collection|集合(表)|
|document|文档(行,row,一条记录)|
|field|字段|
|index|索引|
|primary key|默认将_id设置为主键|


自带db
* admin : 权限高,"root"用户,若将一个用户添加到这个数据库,该用户自动继承所有数据库的权限.某些特定的命令也只能在该库运行.
* local :
* config : 进行分片设置时,在内部使用,用于保存分片的信息

### document

键值对有序
区分类型,大小写
键不可相同

### 数据类型

// TODO


## 安装

略

## 数据目录

安装完成后需要创建数据库目录
目录文件夹应该放在根目录下
`/data/db`

> windows : `C:/data/db`

|参数|说明|
|:-|:-|
|--bind_ip|服务ip|
|--logpath|log文件|
|--logappend|log写入方式|
|--dbpath|数据库路径|
|--port|端口,默认是27017|
|--servicename|服务名|
|--serviceDisplayName|指定服务名,多个服务同时运行时|
|--install|指定作为一个windows服务安装|

## 运行MongoDB服务


```
monodb
```

标准URI连接:
```
mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]
```
例:
```
mongodb://testname:testpwd@127.0.0.1:27017/testdb
```

基本格式

`username:password@hostname/dbname`

### MongoDB Shell

```
mongo
```
> 默认使用`test`数据库

## 基本命令

### 数据库

使用/新建数据库
```
use [dbname]
```
存在则切换,不存在则新建

删除数据库

```
db.dropDatabase()
```

查看所有数据库

```
show dbs
```
通常新建的数据库不能被list,需要向其中插入一条数据

查看当前数据库名
```
db
```

### 集合

文档的数据结构和JSON基本一样。
所有存储在集合中的数据都是BSON格式。
BSON是一种类json的一种二进制形式的存储格式,简称Binary JSON。

查看所有集合
```
show tables
```
删除集合
```
db.collection.drop()
```
### 基本操作

#### 插入(增)
```
document=({age:26})
db.collectionName.insert(document)
```
集合不在该数据库中, MongoDB 会自动创建该集合并插入文档

```
db.col.save(document)
```
插入文档也可以使用save(),区别在于是否指定`_id`字段,不指定则类似于和insert()


* db.collection.insertOne():向指定集合中插入一条文档数据
* db.collection.insertMany():向指定集合中插入多条文档数据


#### 删除(删)

执行remove()函数前先执行find()命令来判断执行的条件是否正确
```
db.collection.remove(
   <query>,
   {
     justOne: <boolean>,
     writeConcern: <document>
   }
)
```
* justOne : 如果设为 true,则只删除一个文档。

```
db.col.remove({'age':16})
db.col.remove({'age':16},true)
# truncate
db.col.remove({)
```

#### 更新(改)

使用 `update()` 和 `save()` 方法来更新集合中的文档

update()
```
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

参数说明：
* query : update的查询条件,类似sql update查询内where后面的。
* update : update的对象和一些更新的操作符(如$,$inc...)等,也可以理解为sql update查询内set后面的
* upsert : 可选,如果不存在update的记录,是否插入objNew,true为插入,默认是false,不插入。
* multi : 可选,默认是false,只更新找到的第一条记录,如果为true,就把按条件查出来多条记录全部更新。
* writeConcern :可选,抛出异常的级别


```
db.col.update({age:26},{$set:{age:18}})
```

save()
通过传入的文档来替换已有文档

```
db.collection.save(
   <document>,
   {
     writeConcern: <document>
   }
)
```



#### 查询(查)

非格式化显示结果
```
db.collection.find(query, projection)
```
query:
and `{"by":"菜鸟教程", "title":"MongoDB 教程"}`
or `{$or:[{key1:value1},{key2:value2}]}`


格式化显示结果
```
db.col.find().pretty()
```

只返回一个文档
```
db.col.findOne()
```

* projection ：可选,使用投影操作符指定返回的键。(0表示不显示 1表示显示).如`{"age":1,_id:0}` 显示age内容,不显示_id内容



## 关系符
```
等于	{<key>:<value>}
不等于	{<key>:{$ne:<value>}}

小于	{<key>:{$lt:<value>}}
小于或等于	{<key>:{$lte:<value>}}

大于	{<key>:{$gt:<value>}}
大于或等于	{<key>:{$gte:<value>}}
```

```
db.col.find({"age":{$lt:40,$gt:18}})
```


## limit() skip()

limit()方法接受一个数字参数,指定从MongoDB中读取的记录条数


```
db.colName.find().limit(NUMBER)
```


skip方法接受一个数字参数,作为跳过的记录条数

```
db.colName.find().skip(NUMBER)
```

* 默认参数为 0
* 当查询时同时使用sort,skip,limit,无论位置先后,最先执行顺序 sort再skip再limit。


> mysql中limit(10,1000)
> db.col.find().skip(10).limit(100)


## sort()
```
db.col.find().sort({key:1})
```
> 1:升序
> -1:降序

## ensureIndex()

索引是特殊的数据结构,索引存储在一个易于遍历读取的数据集合中,索引是对数据库表中一列或多列的值进行排序的一种结构

使用 ensureIndex() 方法来创建索引

```
db.colName.ensureIndex({key:1},{option:value})
```

* 1:按升序创建索引
* 可以设置使用多个字段创建索引{key1:1,key2:-1}

|参数|类型|说明|
|:-|:-|:-|
|background|Boolean|建索引过程会阻塞其它数据库操作,background可指定以后台方式创建索引,即增加 "background" 可选参数。 "background" 默认值为false。|
|unique|Boolean|建立的索引是否唯一。指定为true创建唯一索引。默认值为false.|
|name|string|索引的名称。如果未指定,MongoDB的通过连接索引的字段名和排序顺序生成一个索引名称。|
|dropDups|Boolean|在建立唯一索引时是否删除重复记录,指定 true 创建唯一索引。默认值为 false.|
|sparse|Boolean|对文档中不存在的字段数据不启用索引；这个参数需要特别注意,如果设置为true的话,在索引字段中不会查询出不包含对应字段的文档.。默认值为 false.|
|expireAfterSeconds|integer|指定一个以秒为单位的数值,完成 TTL设定,设定集合的生存时间。|
|v|index version|索引的版本号。默认的索引版本取决于mongod创建索引时运行的版本。|
|weights|document|索引权重值,数值在 1 到 99,999 之间,表示该索引相对于其他索引字段的得分权重。|
|default_language|string|对于文本索引,该参数决定了停用词及词干和词器的规则的列表。 默认为英语|
|language_override|string|对于文本索引,该参数指定了包含在文档中的字段名,语言覆盖默认的language,默认值为 language.|

```
db.col.ensureIndex({key1:1,key1:1}, {background: true})
```

## aggregate()

聚合

```
db.colName.aggregate(aggregate_operation)
```
例:
```
db.mycol.aggregate([
  {
    $group : {
      _id : "$by_user", 
      num : {$sum : 1}
      }
  }
  ])
  ```
|	参数	|	说明	|	例	|
|	:-	|	:-	|	:-	|
|	$push	|	在结果文档中插入值到一个数组中。	|	db.col.aggregate([{$group : {_id : "$by_user", url : {$push: "$url"}}}])	|
|	$addToSet	|	在结果文档中插入值到一个数组中,但不创建副本。	|	db.col.aggregate([{$group : {_id : "$by_user", url : {$addToSet : "$url"}}}])	|
|	$first	|	根据资源文档的排序获取第一个文档数据。	|	db.col.aggregate([{$group : {_id : "$by_user", first_url : {$first : "$url"}}}])	|
|	$last	|	根据资源文档的排序获取最后一个文档数据	|	db.col.aggregate([{$group : {_id : "$by_user", last_url : {$last : "$url"}}}])	|


## 管道

* $project：修改输入文档的结构。可以用来重命名、增加或删除域,也可以用于创建计算结果以及嵌套文档。
* $match：用于过滤数据,只输出符合条件的文档。$match使用MongoDB的标准查询操作。
* $limit：用来限制MongoDB聚合管道返回的文档数。
* $skip：在聚合管道中跳过指定数量的文档,并返回余下的文档。
* $unwind：将文档中的某一个数组类型字段拆分成多条,每条包含数组中的一个值。
* $group：将集合中的文档分组,可用于统计结果。
* $sort：将输入文档排序后输出。
* $geoNear：输出接近某一地理位置的有序文档。

```
db.article.aggregate(
    { $project : {
        title : 1 ,
        author : 1 ,
    }}
 );
 ```

## 备份&恢复

备份
```
mongodump -h <hostname><:port> -d <dbname> -o <path>
```



mongodump --host HOST_NAME --port PORT_NUMBER
mongodump --dbpath DB_PATH --out BACKUP_DIRECTORY
mongodump --collection COLLECTION --db DB_NAME

恢复

```
mongorestore -h <hostname><:port> -d <dbname> <path>
```

--drop：
恢复的时候,先删除当前数据,然后恢复备份的数据

--dir：
指定备份的目录
你不能同时指定`path`和 `--dir` 选项。


> 备份和恢复时,省略所有参数,则会备份到主目录下.