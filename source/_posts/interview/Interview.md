---
title: Interview
date: 2018-02-25
---
<!-- TOC -->

- [基础](#基础)
    - [OO](#oo)
    - [static](#static)
    - [序列化](#序列化)
    - [泛型](#泛型)
        - [通配符](#通配符)
    - [I/O](#io)
    - [反射 内省](#反射-内省)
    - [异常](#异常)
- [集合](#集合)
- [JVM](#jvm)
- [GC](#gc)
- [并发编程](#并发编程)
- [缓存](#缓存)
- [分布式](#分布式)
- [Framework](#framework)
    - [SpringMVC](#springmvc)
    - [Hibernate](#hibernate)
    - [MyBatis](#mybatis)
    - [Spring](#spring)
    - [Struts2](#struts2)
- [设计模式](#设计模式)
- [数据库](#数据库)
- [计算机网络](#计算机网络)
- [数据结构和算法](#数据结构和算法)

<!-- /TOC -->

# 基础

* java中==和equals和hashCode的区别
* int、char、long各占多少字节数
* int与integer的区别
* String、StringBuffer、StringBuilder区别
* final，finally，finalize的区别
* string 转换成 integer的方式及原理
string->integer
parseInt()
integer->string

* 什么是内部类？内部类的作用
* 闭包和局部内部类的区别

## OO

* 对java多态的理解
* 抽象类和接口区别
* 抽象类的意义
* 抽象类与接口的应用场景
* 抽象类是否可以没有方法和属性？
* 接口的意义

## static

* 父类的静态方法能否被子类重写
* 静态属性和静态方法是否可以被继承？是否可以被重写？以及原因？
* 静态内部类的设计意图
* 成员内部类、静态内部类、局部内部类和匿名内部类的理解，以及项目中的应用

## 序列化

* Serializable 和Parcelable 的区别
* 序列化的方式

## 泛型

* 将异常提前至编译期
* 使用泛型的集合只能存放同一种数据类型(编译器进行泛型检查)
* 泛型擦除:泛型只存在编译阶段,运行阶段不存在泛型(此时类型已经确定)
* 使用场景:泛型类, 泛型方法, 泛型集合
* 数组不支持泛型

> 泛型类一般使用字母 T 作为泛型标志
> 泛型集合通常使用T(Type),K(Key),V(Value),E(Element)等作为标志,如Map<K, V>
> `<>`中类型存在父子关系,但泛型List<Number>和List<Integer>不具有

* 泛型中extends和super的区别

### 通配符

类型通配符一般是使用`?`代替方法具体的类型实参,即`<>`中是什么类型方法都可以处理.
类型通配符上限和类型通配符下限,如`<? extends Number>`和`<? super Number>`

使用了`<?>`的集合,使用add,get以及集合拥有的其他方法,因为此时不能确定类型,
使用了上限通配符的集合等,不能向集合中添加元素,即使用`add()`等方法(`add(null)`除外)
下限则无此限制,但不能从其中取数据,即使用`get()`方法

泛型中使用`instanceof`判断类型,只能使用`<?>`,如
```Java
public static void check(List<?> list) {
    if (list instanceof ArrayList<?>) {
    }
}
```

## I/O

java中有几种类型的流？JDK为每种类型的流提供了一些抽象类以供继承, 请说出他们分别是哪些类？

什么是java序列化, 如何实现java序列化？


## 反射 内省

[反射](./Java/reflect.md)
[内省](./Java/内省.md)

## 异常

* ClassCastException


# 集合

1: HashMap和Hashtable的区别。
2:Collection 和 Collections的区别。
3: List, Set, Map是否继承自Collection接口?
4:说出ArrayList,Vector, LinkedList的存储性能和特性？
5:你所知道的集合类都有哪些？主要方法？
jdk集合框架
    ArrayList
    LinkedList
    HashMap
    Hashtable
    HashSet
    LinkedHashSet
    ConcurrentHashMap
    Vector
    这些集合是怎样实现的, 底层使用哪些数据结构实现

# JVM

java内存模型
jvm的类加载机制
垃圾收集机制
编译运行与解释运行的区别
各个垃圾收集器的区别
jvm常用参数配置

# GC

# 并发编程

线程的生命周期,线程各个状态之间的切换
ReentrantLock,ArrayBlockingQueue,LinkedBlockingQueue源码
自旋锁
volatile,内存屏障
线程池
内核态与用户态
lock(),tryLock(),lockInterupttibly()的区别
jdk线程池实现原理（ThreadPoolExecutor如何复用线程）

* 进程和线程的区别

# 缓存

redis或mencached,
redis有哪些数据结构（string, list, hash, zset, set）, redis对比mencached有什么优缺点（有数据类型, 原生支持集群）, 由于有数据类型所以会占用更多的空间

# 分布式

CAP: 是指Consistency一致性，Availability可用性，Partition Tolerance分区容忍性

# Framework

## SpringMVC

    Servlet的生命周期
    生命周期,运行流程
    如何处理request
    各大组件的作用

## Hibernate

## MyBatis

## Spring
    Spring aop的实现原理, 了解cglib和jdk动态代理
    Spring容器的生命周期
    Spring bean的声明周期
    Spring Ioc, 反射机制

## Struts2

# 设计模式

简单工厂模式,工厂方法模式,抽象工厂模式,策略模式,单例模式,代理模式,装饰器模式,观察者模式,适配器模式

# 数据库

数据库(以mysql为例)
    mysql的各个引擎
    索引类型
    InnoDB引擎的索引实现,了解B+树和B树
    聚簇索引和非聚簇索引
    mysql的四个事务隔离级别,事务的四个
    mysql如何解决幻读,如何解决不可重复读, 了解MVCC和next-key锁
    sql优化, 索引覆盖,延迟关联

# 计算机网络

    TCP三次握手,四次挥手
    TCP流量控制,拥塞控制
    cookie,session的区别
    TCP粘包拆包
    IP,http,https
    在浏览器输入一个网址发生了什么

# 数据结构和算法

    动态规划, 笔试题经常出现的感觉
    几大排序算法,常用的数据结构（看算法那本教材感觉挺不错）

--------------------------------------------------

1. java HashMap的原理。

是怎么存放的, 发生hash碰撞怎么做, 为什么get请求的算法复杂度会接近O(1)
了不了解ConcurrentHashMap
原理是什么, 如果答出了分段锁之类的, 再问一下java7和java8有什么不一样, 因为在8的时候做了很多改变, 已经不是以前那种Segement了, 改用了node数组, 扩容是怎么扩的, 扩容也是分段扩的, 并不是整个桶一起扩的。

2. java的运行机制

例如java文件编译成class文件, 由classloader载入->运行
问一下classloader的双亲委派模型, 为什么要这样做（可以保证java的一些重要类如Object一定是rt.jar中的, 保证系统的稳定性）, 有哪些问题（模型本身决定的, 例如基础类要掉回用户代码）, 后来怎么解决了（线程上下文类加载器）
问一下java的内存模型, 堆和栈这些
gc怎么确定哪些是垃圾（可达性分析）, 哪些是根？
问一下java的垃圾回收
比如了解哪些算法啊, 最好可以说说每个算法的算法流程, 例如CMS的第一步是初始标记-并发标记-重新标记-并发清理-并发重置 每个算法的优缺点啊, 怎么简单的解决啊（简单回答就好了, 例如增加堆的大小, 增加后台线程, 提前开始并发周期等）, 也可以问一下有没有了解G1收集器这些, G1的流程, 相比CMS有哪些优势。

java 的对象分分配策略
在Eden中, 大对象直接进入老年代, 长期存活的对象进入老年代, 动态年龄分配, 空间分配担保

会哪些java工具例如jinfo, jstat, jstack, jcmd等

3. 谈谈设计模式

用过什么设计模式, 谈谈什么时候该用这些模式, 再问一下, 其他的设计模式

4. java多线程问题

怎么创建一个线程, 怎么启动一个线程, 为什么要用多线程技术, 有什么优点（异步运行提高响应速度）缺点（线程上下文的切换）？
怎么确定线程数, 根据cpu密集型, 还是io密集型决定
线程池问题, 在java中怎么创建一个线程池（Executors, 或者new ThreadPoolExecutor（））, 线程池可以解决什么问题（减少了线程创建销毁的时间）, 可以继续问一下一个线程被提交到线程池会做些什么判断（core线程池满？->等待队列满？->整个线程池满？->饱和策略）
线程同步问题, 一般都会回答synchronized跟lock, 他们之间的区别（lock可中断, 可超时, 可尝试获取锁）, 性能问题谁的性能更好？
问一下volatile的并语义, 是不是线程安全, 适合哪些场景
有没有看过lock的源码, lock的原理是什么？通过AQS（AbstractQueuedSynchronizer）来维护一个int, 是否了解Lock.Condition。
在java虚拟机层面上做了哪些锁优化（锁的种类）,偏向锁, 轻量级锁, 重量级锁
有没有听说过悲观锁和乐观锁, 乐观锁CAS的原理是什么, 有什么缺点（ABA问题）, 怎么解决, 还有就是两个CAS在一起能否保证线程安全（不能）

5. 是否了解计算机网络

有没有听说过七层协议, ip跟tcp分别属于哪一层。
有没有用过ping命令？ping命令用的是什么协议（icmp）, 有没有经过tcp层？除了判断可达性还有哪些实用（Traceroute）, 原理是什么
有没有听过tcp三次握手, 过程是什么, 为什么要三次握手？那四次挥手呢？
6. 问一下有没有用过spring, springmvc

spring可以为我们解决什么问题（业务解耦）, springMVC在一个请求过来是怎么找到相应的处理器（
简单回答即可, 例如所有请求都会被DispatcherServlet拦截->通过request找到handler->通过handler找到HandlerAdapter）

7. 问一些简单的mysql知识 INT（11）的11代表什么, varchar(11)中的11呢

9.
是否了解mysql的读写分离, 是否了解mysql的分库分表, 会带来什么问题（不能join）

10.
mysql事务隔离级别（读为提交, 读已提交, 可重复读, 可串行化）