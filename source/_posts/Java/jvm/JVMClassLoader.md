---
title: JVM1:类加载机制
date: 2017-11-06
tags:
- Java
- JVM
---
<!-- TOC -->

- [JVM工作原理](#jvm工作原理)
- [类的加载](#类的加载)
- [双亲委托模型](#双亲委托模型)
- [JVM内存分配](#jvm内存分配)

<!-- /TOC -->
# JVM工作原理

JVM 主要由 `ClassLoader` 和 `执行引擎` 两子系统组成.

![](https://raw.githubusercontent.com/LuVx21/hexo/master/source/_posts/Java/img/jvmme.png)

任何一个Java类的main函数运行都会创建一个JVM实例, 当main函数结束时, JVM实例也就结束了.
JVM实例启动时默认启动几个守护线程, 比如: 垃圾回收的线程, 而 main 方法的执行是在一个单独的非守护线程中执行的.只要母线程结束, 子线程就自动销毁, 只要非守护main 线程结束JVM实例就销毁了.

那么在Java类main函数运行过程中, JVM的工作原理如下:

1. 根据系统环境变量, 创建装载JVM的环境与配置；
2. 寻找JRE目录, 寻找jvm.dll, 并装载jvm.dll；
3. 根据JVM的参数配置, 如: 内存参数, 初始化jvm实例；
4. JVM实例产生一个引导类加载器实例(Bootstrap Loader), 加载Java核心库, 然后引导类加载器自动加载扩展类加载器(Extended Loader),加载Java扩展库, 最后扩展类加载器自动加载系统类加载器(AppClass Loader), 加载当前的Java类；
5. 当前Java类加载至内存后, 会经过验证、准备、解析三步, 将Java类中的类型信息、属性信息、常量池存放在方法区内存中, 方法指令直接保存到栈内存中, 如: main函数；
6. 执行引擎开始执行栈内存中指令, 由于main函数是静态方法, 所以不需要传入实例, 在类加载完毕之后, 直接执行main方法指令；
7. main函数执行主线程结束, 随之守护线程销毁, 最后JVM实例被销毁；

# 类的加载

类的加载指的是将类的`.class`文件中的二进制数据读入到内存中,将其放在运行时数据区的方法区内,然后在堆区创建一个java.lang.Class对象,用来封装在方法区内的数据结构.
类的加载最终是在堆区内的Class对象,Class对象封装了类在方法区内的数据结构,并且向开发者提供了访问方法区内的数据结构的接口.

怎样加载一个类:

1. 命令行启动应用时候由JVM初始化加载
2. 通过`Class.forName()`方法动态加载
3. 通过`ClassLoader.loadClass()`方法动态加载

类加载器结构
![](https://raw.githubusercontent.com/LuVx21/hexo/master/source/_posts/Java/img/classloader.png)

只有当一个类要使用的时候,类加载器才会加载这个类并初始化

* 启动类加载器:Java核心库
* 扩展类加载器
* 应用类加载器

一个类被加载,当且仅当其某个静态成员（静态方法等、构造器）被调用时发生.
加载一个类时,其内部类不会同时被加载。


# 双亲委托模型


# JVM内存分配

内存结构
![](https://raw.githubusercontent.com/LuVx21/hexo/master/source/_posts/Java/img/classloader0.png)

类声明周期
![](https://raw.githubusercontent.com/LuVx21/hexo/master/source/_posts/Java/img/classload.png)


2. java的运行机制

例如java文件编译成class文件, 由classloader载入->运行
问一下classloader的双亲委派模型, 为什么要这样做(可以保证java的一些重要类如Object一定是rt.jar中的, 保证系统的稳定性), 有哪些问题(模型本身决定的, 例如基础类要掉回用户代码), 后来怎么解决了(线程上下文类加载器)
问一下java的内存模型, 堆和栈这些
gc怎么确定哪些是垃圾(可达性分析), 哪些是根?
问一下java的垃圾回收
比如了解哪些算法啊, 最好可以说说每个算法的算法流程, 例如CMS的第一步是初始标记-并发标记-重新标记-并发清理-并发重置 每个算法的优缺点啊, 怎么简单的解决啊(简单回答就好了, 例如增加堆的大小, 增加后台线程, 提前开始并发周期等), 也可以问一下有没有了解G1收集器这些, G1的流程, 相比CMS有哪些优势.

java 的对象分分配策略
在Eden中, 大对象直接进入老年代, 长期存活的对象进入老年代, 动态年龄分配, 空间分配担保

会哪些java工具例如jinfo, jstat, jstack, jcmd等