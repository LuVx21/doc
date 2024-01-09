---
title: MySQL:一些知识点
date:
tags:
- MySQL
---
<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [复制表](#复制表)
- [库名,表名长度限制](#库名表名长度限制)
- [忽略插入](#忽略插入)
- [插入更新](#插入更新)
- [记录操作](#记录操作)
- [执行文件](#执行文件)
- [导入导出数据](#导入导出数据)
- [查询添加序号](#查询添加序号)
- [阅读](#阅读)

<!-- /TOC -->
</details>

## 复制表

```sql
create table if not exists user_new (like user);
create table if not exists user_new select * from user;
```
可以拷贝一个表中其中的一些字段:
```sql
create table newadmin as
(
    select username, password from admin
)
```
可以将新建的表的字段改名:
```sql
create table newadmin as
(
    select id, username as uname, password as pass from admin
)
```
可以拷贝一部分数据:
```sql
create table newadmin as
(
    select * from admin where left(username,1) = 's'
)
```
可以在创建表的同时定义表中的字段信息:
```sql
create table newadmin
(
    id integer not null auto_increment primary key
)
as
(
    select * from admin
)
```

## 库名,表名长度限制

| 数据库类型 | 库名 | 表名                       | 字段名    |
| :--------- | :--- | :------------------------- | :-------- |
| MySQL      | 64   | 64个字符                   | 64个字符  |
| SQLSERVER  |      | 128个字符, 临时表116个字符 | 128个字符 |
| Oracle     |      | 30个字符                   | 30个字符  |

## 忽略插入

当主键或者唯一性约束冲突时, 不执行插入操作, 不抛出异常

```sql
insert ignore into user(user_id, user_name)
values(1, 'foobar');
```

## 插入更新

当主键或者唯一性约束冲突时, 执行更新操作

```sql
insert into user(user_id, user_name)
values(1, 'foobar')
on duplicate key update
user_name = values(user_name);
```

## 记录操作

实现与Oracle中的`spool`相似的功能

```shell
## 方式1
mysql -u root -p --tee=/mysql/log.log
## 方式2
tee log.log
notee
## 方式3
\T log.log
\t
```

## 执行文件

```shell
## mysql –u root –p123456 -Dtest</home/user/1.sql
\. <path>
source <path>
```

## 导入导出数据

```sql
load data infile '/tmp/user.csv'
into table user
fields terminated by ',' optionally enclosed by '"' escaped by '"'
lines terminated by '\r\n';
```

```sql
select * from user
into outfile '/tmp/user.csv'
fields terminated by ',' optionally enclosed by '"'
lines terminated by '\r\n';
```

配置:

> `show global variables like '%secure_file_priv%';`
>
> 修改文件`my.ini`: 添加`secure_file_priv=''`

## 查询添加序号

```sql
select
    ( @i := @i + 1 ) as `no`,
    a.*
from
    user a,
    ( select @i := 0 ) b
order by
    a.id
limit 0, 10;
```

## 阅读

TokuDB 适用于数据写多读少, 而且数据量比较大
支持在线加减字段, 在线创建索引, 锁表时间很短

TiDB是PingCAP公司推出的开源分布式关系型数据库, 结合了传统的 RDBMS 和 NoSQL 的最佳特性. TiDB 兼容 MySQL, 支持无限的水平扩展, 具备强一致性和高可用性.
TiDB 的目标是为 OLTP (Online Transactional Processing) 和 OLAP (Online Analytical Processing) 场景提供一站式的解决方案.

