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


# 缓存一致性协议


## 哈希一致性算法

对key进行哈希运算,从而确定键值对存储在哪个缓存服务器
