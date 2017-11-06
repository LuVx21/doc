---
title: Java集合
tags:
- Java
---

# java collection

@(Java)[hashset]


[TOC]


## map

### hashmap

put()时,key重复则覆盖现有value,返回原来的value,否则返回null.

底层实际上是一个数组,key以string形式存在,以key计算value的存储位置.
同一位置处的value数量达到8后,会重构为红黑树


## List

Collection <-- List

* 元素有序
* 可以重复


## Set

Collection <-- Set

* 元素无序
* 不能重复

> Set不保证插入有序是指Set这个接口的规范,实现类只要遵循这个规范即可,也能写出有序的set

### HashSet

Collection <-- Set <-- HashSet

底层以hashmap形式存在,
主要功能方法实际上都是调用hashmap的方法
基本上可以说HashSet就是利用HashMap来实现的

add()其实是调用hashmap的put,在遇到key重复时,返回的是false,否则返回true
set中的元素实质上是hashmap的key,所有元素的value都指向Object对象,
由于使用不到hashmap的value,所以set不会为每个value分配一个内存空间,因而内存占用少

size()
isEmpty()
remove()
contains()
clear()
等也都是内部调用hashmap的方法.

> 可以推测,TreeSet内部也是利用TreeMap实现
> HashSet底层声明了一个HashMap,HashSet做了一层包装,操作HashSet里的元素时其实是在操作HashMap里的元素.

### TreeSet

Collection <-- Set <-- TreeSet

同样,TreeSet就是利用TreeMap来实现的
TreeSet是里面的内容有序（按照一定规则排序）,但不是指元素的添加顺序

TreeSet构造函数:
```Java
TreeSet --> TreeMap --> NavigableMap(接口)
<-->
NavigableMap m = new TreeMap<>();
```
 常用方法同HashSet类似,封装了TreeMap的方法

> TreeSet底层也是声明了一个TreeMap,操作TreeSet里的元素其实是操作TreeMap里的元素.

### LinkedHashSet

有序