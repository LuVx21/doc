---
title: 执行计划
date:
tags:
-
---

<!-- TOC -->


<!-- /TOC -->

`explain select * from user where userid >= 2\G;`

|字段|说明|
|:---|:---|
|id: 1||
|select_type: SIMPLE|查询类型:简单,联合,字查询|
|table: user|表名|
|partitions: NULL||
|type: range|检索类型,优劣依次为:system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL|
|possible_keys: PRIMARY|可能使用的索引|
|key: PRIMARY|实际使用的索引|
|key_len: 8|最长索引宽度,其值越小越好|
|ref: NULL|显示哪个字段或常数与key一起被使用|
|rows: 2|遍历多少条数据找到结果|
|filtered: 100.00||
|Extra: Using where|执行状态说明|

> const代表一次就命中,ALL代表全表扫描后才确定结果,通常得保证查询至少达到range级别,最好能达到ref
> Extra如果是Using filesort或Using temporary则需要对SQL进行优化