---
title: JVM2:内存结构
date: 2017-11-06
tags:
- Java
- JVM
---
<!-- TOC -->

- [内存结构](#内存结构)
    - [堆内存(Heap)](#堆内存heap)
    - [方法区(Method Area)](#方法区method-area)
    - [运行时常量池](#运行时常量池)
    - [程序计数器(Program Counter Register)](#程序计数器program-counter-register)
    - [JVM栈(JVM Stacks)](#jvm栈jvm-stacks)
    - [本地方法栈(Native Method Stacks)](#本地方法栈native-method-stacks)
    - [直接内存](#直接内存)
- [比较](#比较)
- [内存溢出的地点](#内存溢出的地点)
- [JVM调优工具](#jvm调优工具)
- [参考](#参考)

<!-- /TOC -->

# 内存结构

![内存结构](./img/jvm01.png)

* 堆内存
    * 年轻代(8:1:1)
        * Eden空间
        * From Survivor
        * To Survivor
    * 老年代
* 方法区
* 栈
    * java虚拟机栈
    * 本地方法栈

1. 方法区和堆是所有线程共享的内存区域
2. java栈、本地方法栈和程序计数器是运行时线程私有的内存区域


## 堆内存(Heap)


Java堆是被所有线程共享的一块内存区域, 在虚拟机启动时创建.此内存区域的唯一目的就是存放对象实例, 几乎所有的对象实例都在这里分配内存.

Java堆是垃圾收集器管理的主要区域, 因此很多时候也被称做“GC堆”
采用分代收集策略,所以细分为新生代和老年代.

堆内存可以是物理上不连续的内存空间, 逻辑上连续即可, 

在堆中没有内存完成实例分配, 并且堆也无法再扩展时, 将会抛出OutOfMemoryError异常

## 方法区(Method Area)

与Java堆一样, 是各个线程共享的内存区域, 它用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译器编译后的代码等数据

有时也被称为'永久代'(PermGen)

Java虚拟机规范对这个区域的限制非常宽松, 除了和Java堆一样不需要连续的内存和可以选择固定大小或者可扩展外, 还可以选择不实现垃圾收集

这个区域的内存回收目标主要是针对常量池的回收和对类型的卸载

当方法区无法满足内存分配需求时, 将抛出OutOfMemoryError异常

![](./img/jvm02.png)

## 运行时常量池

是方法区的一部分,存放编译期生成的各种字面量和符号引用
当JVM运行的时候会将这些常量池的信息加载进方法区

当方法区无法满足内存分配需求时,抛出OutOfMemoryError

## 程序计数器(Program Counter Register)

一块较小的内存空间, 属于线程私有
字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要执行的字节码指令, 分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖这个计数器来完成.
如果线程正在执行一个java方法,这个计数器记录的是正在执行的虚拟机字节码指令的地址;如果是Naive方法,则计数器为空;
多线程时, 存在多个程序计数器.

此内存区域是唯一一个在Java虚拟机规范中没有规定任何OutOfMemoryError情况的区域.

## JVM栈(JVM Stacks)

线程私有, 生命周期与线程相同

虚拟机栈描述的是Java方法执行的内存模型:每个方法被执行的时候都会同时创建一个栈帧(Stack Frame)用于存储局部变量表、操作栈、动态链接、方法出口等信息.
每一个方法被调用直至执行完成的过程, 就对应着一个栈帧在虚拟机栈中从入栈到出栈的过程.

对这个区域规定了两种异常状况:
如果线程请求的栈深度大于虚拟机所允许的深度, 将抛出StackOverflowError异常;
如果虚拟机栈可以动态扩展(当前大部分的Java虚拟机都可动态扩展, 只不过Java虚拟机规范中也允许固定长度的虚拟机栈), 当扩展时无法申请到足够的内存时会抛出OutOfMemoryError异常.

> 栈帧：一个栈帧随着一个方法的调用开始而创建，这个方法调用完成而销毁。栈帧内存放着方法中的局部变量，操作数栈等数据

## 本地方法栈(Native Method Stacks)

虚拟机栈为虚拟机执行Java方法(也就是字节码)服务, 
而本地方法栈则是为虚拟机使用到的Native方法服务

异常抛出类型和JVM栈相同

## 直接内存

不是虚拟机运行时数据区的一部分，也不是Java虚拟机规范中定义的内存区域
但是这部分内存也被频繁的使用，而且也可能导致OutOfMemoryError异常出现

# 比较

* 栈内存用来存储局部变量和方法调用。
* 堆内存用来存储Java中的对象。无论是成员变量，局部变量，还是类变量，它们指向的对象都存储在堆内存中。


# 内存溢出的地点

`Exception in thread “main”: java.lang.OutOfMemoryError: Java heap space`

原因: 对象不能被分配到堆内存中

`Exception in thread “main”: java.lang.OutOfMemoryError: PermGen space`

原因: 类或者方法不能被加载到持久代.它可能出现在一个程序加载很多类的时候, 比如引用了很多第三方的库;

`Exception in thread “main”: java.lang.OutOfMemoryError: Requested array size exceeds VM limit`

原因: 创建的数组大于堆内存的空间

`Exception in thread “main”: java.lang.OutOfMemoryError: request <size> bytes for <reason>. Out of swap space?`

原因: 分配本地分配失败.JNI、本地库或者Java虚拟机都会从本地堆中分配内存空间.

`Exception in thread “main”: java.lang.OutOfMemoryError: <reason> <stack trace>(Native method)`

原因: 同样是本地方法内存分配失败, 只不过是JNI或者本地方法或者Java虚拟机发现

`java.lang.OutOfMemoryError: unable to create new native thread`

原因: 创建了太多的线程，而能创建的线程数是有限制的，导致了异常的发生

# JVM调优工具

1. jps:查询正在运行的JVM进程
2. jstat:实时显示本地或远程JVM进程中类装载、内存、垃圾收集、JIT编译等数据
3. jinfo:查询当前运行着的JVM属性和参数的值
4. jmap:显示当前Java堆和永久代的详细信息
5. jhat:分析使用jmap生成的dump文件
6. jstack:生成当前JVM的所有线程快照，线程快照是虚拟机每一条线程正在执行的方法,目的是定位线程出现长时间停顿的原因。
7. jconlose:jvm控制台,图像化显示堆栈等使用情况,可以手动进行GC,非常实用

* `-Xmx`:最大堆内存
* `-Xms`:最小堆内存, 通常设置成跟最大堆内存一样，减少GC
* `-Xmn`:设置年轻代大小,官方推荐设置为堆的`3/8`
* `-Xss`:指定线程的最大栈空间, 此参数决定了java函数调用的深度, 值越大调用深度越深, 若值太小则容易出栈溢出错误(StackOverflowError)
* `-XX:PermSize`:指定方法区(永久区)的初始值,默认是物理内存的1/64， 在Java8永久区移除, 代之的是元数据区， 由-XX:MetaspaceSize指定
* `-XX:MaxPermSize`:指定方法区的最大值, 默认是物理内存的1/4， 在java8中由-XX:MaxMetaspaceSize指定元数据区的大小
* `-XX:NewRatio=n`:年老代与年轻代的比值，-XX:NewRatio=2, 表示年老代与年轻代的比值为2:1
* `-XX:SurvivorRatio=n`:Eden区与Survivor区的大小比值，-XX:SurvivorRatio=8表示Eden区与Survivor区的大小比值是8:1:1，因为Survivor区有两个(from, to)

# 参考

[jvm系列(二):JVM内存结构](http://www.ityouknow.com/jvm/2017/08/25/jvm-memory-structure.html)