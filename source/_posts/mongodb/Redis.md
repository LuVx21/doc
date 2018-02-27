---
title: Redis
date: 2017-05-11
tags:
- Redis
- NoSQL
---

<!-- TOC -->

- [关于](#关于)
- [配置](#配置)
- [使用](#使用)
- [命令](#命令)
    - [共有命令](#共有命令)
- [数据类型](#数据类型)
    - [String](#string)
    - [list](#list)
    - [hash](#hash)
    - [set](#set)
    - [zset](#zset)
- [调用Redis](#调用redis)
- [持久化](#持久化)
- [主从复制](#主从复制)

<!-- /TOC -->

# 关于

使用场景:

* 分布式缓存
* 分布式session
* 数据量大# 高并发的场景下

特点:

* key-value形式存储
* 

# 配置

config命令
```
#获取配置信息
config get settingname
config set settingname value
```

设置密码
```
config set requirepass 1121
redis-cli -h 127.0.0.1 -p 6379 -a 1121
```

配置后台运行
redis.conf
```
# daemonize no
daemonize yes
```

# 使用

启动服务

```
redis-server redis.conf
```

关闭服务
```
redis-cli shutdown
```

进入命令行
```
redis-cli -h host -p port -a password
```

> 默认监听`6379`端口

# 命令

格式
```shell

```

## 共有命令

```shell
# 取出所有key
keys *
# 支持正则表达式
keys "foo*"
# 查看数据类型
type <key>
# 重命名key
rename <key>
# 追加value
append <key>
# 判断存在
exists <key>
set: 插入键值, 存在则覆盖
setnx: 插入键值, 存在不覆盖
mset: 批量插入键值, 覆盖
msetnx: 批量插入键值, 不覆盖
getset <key>: 取值并设置新值

incr <key>: 增1
decr <key>: 减1
incrby <key> <i>: 增i
decrby <key> <i>: 减i
```

```shell
# 键值对数量
dbsize
# 清空数据库
flushdb
# 服务信息
info
```

# 数据类型

> 不能存储中文, 存储unicode可以实现间接存储中文

## String

set
get
del


incr:递增
decr:递减
incrby:
decrby:

nil:空

## list

基于双向循环链表实现, 栈

lpush:入栈
rpush:入栈
lpop:
rpop:
lrange obj 0 -1:出栈, 全部取出
llen

## hash



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



# 调用Redis

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
