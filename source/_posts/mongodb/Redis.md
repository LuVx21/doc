---
title: Redis
date: 2017-05-11
tags:
- Redis
---

<!-- TOC -->

- [关于](#关于)
- [配置](#配置)
- [数据类型](#数据类型)
    - [String](#string)
    - [list:基于双向循环链表实现, 栈](#list基于双向循环链表实现栈)
    - [Hash](#hash)
    - [set](#set)
    - [zset](#zset)
- [共有命令](#共有命令)
- [持久化](#持久化)
- [主从复制](#主从复制)

<!-- /TOC -->



# 关于

* 分布式缓存
* 分布式session
* 数据量大, 高并发的场景下



# 配置


配置后台运行
redis.conf
```
# daemonize no
daemonize yes
```

启动

```
redis-server redis.conf
```

关闭
```
redis-cli shutdown
```

# 数据类型

> 不能存储中文, 存储unicode可以实现

## String
set
get
del


incr:递增
decr:递减
incrby:
decrby:

nil:空

## list:基于双向循环链表实现, 栈
lpush:入栈
rpush:入栈
lpop:
rpop:
lrange obj 0 -1:出栈, 全部取出
llen

## Hash
hset
hget

hmset
hmget
hgetall
hdel
hincrby
hlen

## set

无序, 不可重复

sadd
srem
smembers
sismember:判断存在, 1:存在, 0:不存在

支持数学集合的运算
sdiff:差集
sinter:交集
sunion:并集

## zset

有序set

zadd
zrange
zrem
zscore
zrevrange


参数:
withscores


# 共有命令

rename
type





#

```java
// 方式1
Jedis jedis = new Jedis("127.0.0.1", 6379);
// 方式2
JedisPool pool = new JedisPool("127.0.0.1", 6379);
Jedis jedis = pool.getResource();


jedis.close();
pool.close();
```

```python

```

# 持久化

|方案|说明|持有化文件|
|:--|:--|:--|
|rdb|间隔时间保存, 默认方案|dump.rdb|
|aof|实时保存|appendonly.aof|

```
# appendonly no
# 使用aof方案
appendonly yes
```


# 主从复制

1. 从-->主:sync
2. 主-->从:dump.rdb
3. 主-->从:发送缓冲获得写命令

> 从一般只读

从redis.conf:
```
slaveof 127.0.0.1 6379
```

访问从
redis-cli -p 6380
