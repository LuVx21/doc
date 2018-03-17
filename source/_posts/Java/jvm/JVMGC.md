---
title: JVM3:垃圾回收机制
date: 2017-11-06
tags:
- Java
- JVM
---
<!-- TOC -->

- [对象是否可回收](#对象是否可回收)
- [四种引用](#四种引用)
- [垃圾回收策略](#垃圾回收策略)
- [垃圾回收器](#垃圾回收器)

<!-- /TOC -->

# 对象是否可回收

1. 引用计数算法

存储对特定对象的所有引用数，也就是说，当应用程序创建引用以及引用超出范围时，JVM必须适当增减引用数。当某对象的引用数为0时，便可以进行垃圾收集。
优点：实现简单、效率高
缺点：很难解决对象之间相互引用问题

2. 可达性分析算法

通过一系列的称为“GC Roots”的对象作为起始点，从这些节点开始向下搜索，搜索所走的路径称为引用链，当一个对象到GC Roots没有任何引用链相连时，则证明此对象是不可用的。
可作为GC Roots的对象包括：虚拟机栈（栈帧的本地变量表）中引用的对象、方法区中类静态属性引用的对象、方法区中常量引用的对象、本地方法栈中JNI（即一般说的是native方法）引用的对象。

# 四种引用

1. 强引用：只要强引用还存在，垃圾收集器永远不会回收掉被引用的对象。
2. 软引用：对软引用关联着的对象，在系统将要发生内存溢出异常之前，将会把这些对象列进回收范围之中进行第二次回收。
3. 弱引用：对弱引用关联着的对象，只能生存到下一次垃圾收集发生之前。
4. 虚引用：对象是否有虚引用，完全不会对其生存时间构成影响，也无法通过虚引用来取得对象实例。关联虚引用唯一目的就是能在对象被收集器回收时收到系统通知

# 垃圾回收策略

标记-清除算法【适用于老年代】
复制算法【适用于对象存活率低的新生代】
分代收集策略
回收方法区（永久代）
垃圾收集器

# 垃圾回收器

垃圾回收器通常是作为一个单独的低级别的线程运行，
不可预知的情况下对内存堆中已经死亡的或者长时间没有使用的对象进行清楚和回收，程序员不能实时的调用垃圾回收器对某个对象或所有对象进行垃圾回收。
回收机制有分代垃圾回收和标记垃圾回收，增量垃圾回收等