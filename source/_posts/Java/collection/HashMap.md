---
title: HashMap
date: 
tags:
- Java
---


底层:数据+链表+红黑树

1. java HashMap的原理.

是怎么存放的, 发生hash碰撞怎么做, 为什么get请求的算法复杂度会接近O(1)
了不了解ConcurrentHashMap
原理是什么, 如果答出了分段锁之类的, 再问一下java7和java8有什么不一样, 因为在8的时候做了很多改变, 已经不是以前那种Segement了, 改用了node数组, 扩容是怎么扩的, 扩容也是分段扩的, 并不是整个桶一起扩的.