---
title: 索引
date:
tags:
-
---

<!-- TOC -->

- [索引](#索引)
- [Index_type](#index_type)
- [索引设置原则](#索引设置原则)
- [索引的使用](#索引的使用)

<!-- /TOC -->

# 索引

普通索引

```sql
create table <table_name> ([...], index <index_name> (<filed1>,<filed2>,...));
create index <index_name> on <table_name>(<filed1>,<filed2>,...);
alter table <table_name> add index <index_name> (<filed1>,<filed2>,...);
```

唯一性索引

```sql
create table <table_name>( [...], unique <index_name> (<filed1>,<filed2>,...) );
create unique index <index_name> on <table_name>(<filed1>,<filed2>,...);
alter table <table_name> add unique <index_name> (<filed1>,<filed2>,...);
```

primary key索引(主键)

```sql
create table <table_name>([...], primary key (<filed1>,<filed2>,...));
alter table <table_name> add primary key (<filed1>,<filed2>,...);
```


删除索引

```sql
drop index <index_name> on <table_name>;
alter table <table_name> drop index <index_name>;
alter table <table_name> drop primary key;
```

```sql
show index from <table_name>;
show keys from <table_name>;
```

|字段|说明|
|:---|:---|
|Table: user|表名|
|Non_unique: 0|非唯一性索引,1则为普通索引|
|Key_name: PRIMARY|索引名称|
|Seq_in_index: 1|索引中的列序列号|
|Column_name: userid|字段名|
|Collation: A|字段以什么方式存储在索引中,A:升序,NULL:无分类|
|Cardinality: 2|索引中唯一值的数目的估计值,数值越大,使用该索引的概率越大|
|Sub_part: NULL|字段是否部分被编入索引,是则为被编入索引的字符数目,否则为NULL|
|Packed: NULL|关键字如何被压缩,无压缩则为NULL|
|Null:|字段是否含有NULL|
|Index_type: BTREE|索引类型|
|Comment:||
|Index_comment:||


# Index_type

BTREE


# 索引设置原则

1. 频繁被用作查询条件的字段应该创建索引
2. 唯一性不明显的字段不适合创建索引
3. 频繁更新的字段不适合创建索引
4. 不会被用于join的字段没必要创建索引

# 索引的使用

索引被用于join和where的结合条件
索引基数越大,效果越好:基数是指字段中具有唯一性数据的粗略数量
使用短索引:存储空间较大的列,在前部分内容中就已经具有明显的唯一性,就可以设置部分编入索引,在创建索引时,字段后指定长度,如uuid(20)
最左前缀
使用联合索引


并不是在任何情况下都需要使用索引
索引文件本身要消耗存储空间,同时索引会增加写操作的负担,写操作时会更新索引

不建议创建索引的情形:
* 数据量较少
* 索引的选择性（Selectivity）=基数/数据量,较小的情况下.`select count(distinct(left(`username`,10)))/count(*) from user;`大于0.31就可以创建前缀索引


索引在某些情况下会失效
生效的场合:<,<=,=,>,>=,between,in, 以及某些时候的like(不以通配符%或_开头的情形)
失效的场合:
* like 以通配符`%`开始
* 使用`<>`,`not in`
* 结合条件中,索引列参与计算,索引列被函数处理
* 字符串于与数字比较,无论是等或不等比较
* or连接的多个条件
* MySQL判断不用索引比用更快

