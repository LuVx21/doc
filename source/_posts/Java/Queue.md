---
title: 队列
date: 2017-11-01
tags:
- Java
---
<!-- TOC -->

- [](#)
- [非阻塞队列](#非阻塞队列)
- [阻塞队列](#阻塞队列)

<!-- /TOC -->
# 

Queue
AbstractQueue

# 非阻塞队列

PriorityQueue 和 ConcurrentLinkedQueue

# 阻塞队列

BlockingQueue:Interface

ArrayBlockingQueue: 一个由数组支持的有界队列。
LinkedBlockingQueue: 一个由链接节点支持的可选有界队列。
PriorityBlockingQueue: 一个由优先级堆支持的无界优先级队列。
DelayQueue: 一个由优先级堆支持的、基于时间的调度队列。
SynchronousQueue: 一个利用 BlockingQueue 接口的简单聚集（rendezvous）机制。