---
title: 分布式缓存
date: 
tags:
- Web
---
<!-- TOC -->

- [分布式系统](#分布式系统)
- [缓存一致性协议](#缓存一致性协议)
    - [哈希一致性算法](#哈希一致性算法)
- [分布式Session](#分布式session)
- [分布式事务](#分布式事务)
- [分布式锁](#分布式锁)

<!-- /TOC -->

# 分布式系统

分布式系统中删除,增加所带来的影响及解决方案

clustered cache
distributed cache
性能
一致性

consistency
scalability
infrastructure

cache buffer


负载均衡方案中有一种就是IP Hash,即针对访问者IP进行Hash运算,从而决定由那台服务器为该用户提供服务.

CAP理论

WEB服务无法同时满足以下3个属性:

* 一致性(Consistency)
* 可用性(Availability)
* 分区容错性(Partition tolerance)

BASE理论

* Basically Available（基本可用）
* Soft state（软状态）
* Eventually consistent（最终一致性）

# 缓存一致性协议


## 哈希一致性算法

对key进行哈希运算,从而确定键值对存储在哪个缓存服务器

# 分布式Session

Session一致性解决方案:

* 客户端存储Session,不常用
* 同步
* 保证请求分配到同一服务器
* 后端统一存储,推荐存在缓存中

```xml
<context:annotation-config/>
<bean class="org.springframework.session.data.redis.config.annotation.web.http.RedisHttpSessionConfiguration"/>
<bean class="org.springframework.data.redis.connection.lettuce.LettuceConnectionFactory"/>
```

https://blog.csdn.net/wojiaolinaaa/article/details/62424642

https://blog.csdn.net/xlgen157387/article/details/57406162

# 分布式事务

https://www.cnblogs.com/savorboard/p/distributed-system-transaction-consistency.html


# 分布式锁
