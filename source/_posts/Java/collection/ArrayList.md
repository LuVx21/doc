---
title: ArrayList
date: 
tags:
- Java
---

<!-- TOC -->


<!-- /TOC -->

* 底层是数组,默认大小为10
* 扩容后,为原来的1.5倍

实现了RandomAccess接口,因此是随机访问模式
使用transient关键字,使底层数组不被序列化
增删慢
改查快

非线程安全,若想应用于多线程环境,可以Collections.synchronizedList()方法
```Java
List<String> synchronizedList = Collections.synchronizedList(list);
```