---
title: 结果集的集合运算
date: 2017-12-08
tags:
- Oracle
---

<!-- TOC -->

- [通用](#通用)
    - [并集](#并集)
    - [交集](#交集)
    - [差集](#差集)
    - [补集](#补集)
- [Oracle特有](#oracle特有)
    - [intersect:交集](#intersect交集)
    - [minus:差集](#minus差集)

<!-- /TOC -->


常见对sql查询结果的集合运算
操作对象是结果集而不是表数据

# 通用

## 并集

union:并集，并排除重复记录
union all:并集，并包含重复记录

通过使用`union`可以解决MySQL不支持`full join`的问题

```sql
select * from t1 left join t2 on t1.id=t2.id
union
select * from t1 right join t2 on t1.id=t2.id
```

mysql不支持full join,
可以通过left join+join + right join实现full join

## 交集

```sql
select
    a.*
from
    (
    select id, data from t1
    union all
    select id, data from t2
    ) a
group by id, data
having count(*) > 1
```

## 差集

也被成为相对补集
其前提是一方不是另一方的子集

`A∪B-B`

```sql
select
    b.*
from
    (
    select
    a.id,
    a.data
    from (
        select id, data from t1
        union
        select id, data from t2
        ) a
    union all
    select id, data from t2
    ) b
group by id,data
having count(*) < 2
```

## 补集

一般指绝对补集,区分于相对补集
绝对补集的前提是一方是另一方的子集

假设下面的其中一个query是另一个的子集,即存在属于关系
执行结果则为`A-B`

```sql
select
    a.*
from
    (
    select id, data from t1
    union all
    select id, data from t2
    ) a
group by id, data
having count(*) < 2
```

如果不存在属于关系,执行结果则为`A+B-A∩B`

> 补集定义:属于全集而不属于某一集


# Oracle特有


## intersect:交集

## minus:差集

```sql
(select * from t1
minus
select * from t2
)
union all
(select * from t2
minus
select * from t1
)
```

![SQLjoin](https://raw.githubusercontent.com/LuVx21/hexo/master/source/_posts/Oracle/img/sqljoin.jpeg)