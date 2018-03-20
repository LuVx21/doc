---
title: Interview
date: 2018-02-25
---
<!-- TOC -->

- [基础](#基础)
    - [关键字](#关键字)
        - [static](#static)
        - [abstract](#abstract)
        - [native](#native)
    - [I/O](#io)
    - [序列化](#序列化)
    - [OO](#oo)
    - [泛型](#泛型)
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




**`==`和`equals`和`hashCode`的区别**

`==`是运算符,比较两个对象的内存地址是否相同
equals是Object类的方法,返回值为boolean型,内部调用的`==`,可以被子类重写
hashCode是Object类的方法,返回值为int型,
对象相同,则hashCode一定相同,反之不成立.
equals方法返回true,则hashCode值也相同(这样符合规范,可以实现不同),
返回false,hashCode也有可能相同,当然,尽可能不同能提高hash表的性能,这其实是减少了哈希碰撞的几率

Set集合中,元素无序且不可重复,其不可重复的实现则是依靠hashCode和equals方法,
首先使用hashCode计算出元素的存储位置,如该位置为空则直接插入,不空则继续使用equals方法比较.

重写equals时总要重写hashCode,否则违反Object.hashCode的通用约定,导致该类无法结合基于散列的集合一起正常使用,如HashMap、HashSet和Hashtable等

**int、char、long各占多少字节数**

分别是4,2,8个字节(byte),每个字节对应8位(bit)
Java采用unicode编码,使用2个字节表示一个字符

**int与integer的区别**

int是Java基本类型,integer是int类型对应的包装类(对象类型),
基于OO编程思想,设计出包装类,方便处理对象类型和基本的转换等操作,如String和基本类型的转换,集合中存储包装类型
类似于intValue()来转换为基本类型,反过来有自动拆装箱机制转换为包装类型

**String、StringBuffer、StringBuilder区别**

[参考](../Java/String.md)

**final,finally,finalize的区别**

  - final:声明类,变量,方法等,使其不可被继承,不可被修改,不可被重写
  - finally:异常处理操作中,遇到异常后必须要处理的操作可放置在finally代码块中
  - finalize:垃圾回收时使用,通常不需要开发者主动显示调用

**string 转换成 integer的方式及原理**

string->integer

```Java
parseInt()
```
integer->string
```Java
+""
Integer.toString()
```

**什么是内部类?内部类的作用**

Java中指定义在类内部的一个类,可以将其定义为static,即成为静态内部类
可以定义在外部类的方法外面, 也可以定义在外部类的方法体中,final或abstract
还可以声明匿名内部类,

普通内部类:
* 使用依赖于外部类实例,其本身持有外部类的实例
* 其内部不可以定义非静态变量,方法
* 可以使用外部静态属性,方法,也可以使用外部非静态属性,方法,实质是内部类中存在一个final修饰的名为`this$0`的外部实例


静态内部类:
* 其对象的使用不依赖于外部类实例,
* 内部可以定义非静态变量,方法,但若想使用此变量,方法,内部类需要实例化
* 也可以定义静态,方法,使用不需要实例化.
* 只能使用外部静态属性,方法.不可以使用外部非静态属性,方法


**闭包和局部内部类的区别**

## 关键字

### static

可以用于修饰类,变量,方法,代码块,被修饰内容全局唯一,只加载一次,不依赖实例对象,生命周期为类级别

* 类: 被修饰的类只能作为内部类,且其中只能访问静态变量和静态方法,不依赖于外部类,区别于普通内部类需在其自身构造函数中构造外部类对象
* 方法: 存在于普通类和静态内部类中,被动执行(被调用执行).不存在于普通内部类中
* 变量: 可以存在于普通类和静态内部类,但前者必须`final static`修饰
* 代码块:在加载类的时候主动执行,只在第一次创建类对象时调用

> 非静态代码块:在创建对象时执行,每创建一个对象都会执行一次.执行顺序参看`jvm类加载`内容
> 静态代码块的共同点:

* 构造方法前执行
* 可以定义多个,

### abstract

用于修饰方法,使得其只有声明而没有实现, 具体在继承了该类的子类中实现.

不能同时使用的修饰符:

* final:不可重写
* private:不可继承
* static:不可重写

以上修饰符的使用都会导致子类无法重写父类的abstract方法.

### native

本地方法, 这种方法和抽象方法及其类似, 它也只有方法声明, 没有方法实现, 与抽象方法不同的是, 它把具体实现移交给了本地系统的函数库, 而没有通过虚拟机, 可以说是Java与其它语言通讯的一种机制.

**父类的静态方法能否被子类重写**

不能.

**静态属性和静态方法是否可以被继承?是否可以被重写?以及原因?**

**静态内部类的设计意图**

**成员内部类、静态内部类、局部内部类和匿名内部类的理解,以及项目中的应用**

## I/O

java中有几种类型的流?JDK为每种类型的流提供了一些抽象类以供继承, 请说出他们分别是哪些类?

什么是java序列化, 如何实现java序列化?

## 序列化

* Serializable 和Parcelable 的区别

* 序列化的方式

## OO

* 对java多态的理解
* 抽象类的意义
* 接口的意义
* 抽象类和接口区别
* 抽象类与接口的应用场景
* 抽象类是否可以没有方法和属性?

## 泛型

[泛型](../Java/泛型.md)

## 反射 内省

[反射](../Java/reflect.md)
[内省](../Java/内省.md)

## 异常

* ClassCastException


# 集合

1: HashMap和Hashtable的区别.
2:Collection 和 Collections的区别.
3: List, Set, Map是否继承自Collection接口?
4:说出ArrayList,Vector, LinkedList的存储性能和特性?
5:你所知道的集合类都有哪些?主要方法?
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

类加载顺序及过程

静态代码块-->非静态代码块-->默认构造方法-->普通方法中的代码块


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

CAP: 是指Consistency(一致性),Availability(可用性),(Partition tolerance)分区容错性

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

1. java HashMap的原理.

是怎么存放的, 发生hash碰撞怎么做, 为什么get请求的算法复杂度会接近O(1)
了不了解ConcurrentHashMap
原理是什么, 如果答出了分段锁之类的, 再问一下java7和java8有什么不一样, 因为在8的时候做了很多改变, 已经不是以前那种Segement了, 改用了node数组, 扩容是怎么扩的, 扩容也是分段扩的, 并不是整个桶一起扩的.

2. java的运行机制

例如java文件编译成class文件, 由classloader载入->运行
问一下classloader的双亲委派模型, 为什么要这样做（可以保证java的一些重要类如Object一定是rt.jar中的, 保证系统的稳定性）, 有哪些问题（模型本身决定的, 例如基础类要掉回用户代码）, 后来怎么解决了（线程上下文类加载器）
问一下java的内存模型, 堆和栈这些
gc怎么确定哪些是垃圾（可达性分析）, 哪些是根?
问一下java的垃圾回收
比如了解哪些算法啊, 最好可以说说每个算法的算法流程, 例如CMS的第一步是初始标记-并发标记-重新标记-并发清理-并发重置 每个算法的优缺点啊, 怎么简单的解决啊（简单回答就好了, 例如增加堆的大小, 增加后台线程, 提前开始并发周期等）, 也可以问一下有没有了解G1收集器这些, G1的流程, 相比CMS有哪些优势.

java 的对象分分配策略
在Eden中, 大对象直接进入老年代, 长期存活的对象进入老年代, 动态年龄分配, 空间分配担保

会哪些java工具例如jinfo, jstat, jstack, jcmd等

3. 谈谈设计模式

用过什么设计模式, 谈谈什么时候该用这些模式, 再问一下, 其他的设计模式

4. java多线程问题

怎么创建一个线程, 怎么启动一个线程, 为什么要用多线程技术, 有什么优点（异步运行提高响应速度）缺点（线程上下文的切换）?
怎么确定线程数, 根据cpu密集型, 还是io密集型决定
线程池问题, 在java中怎么创建一个线程池（Executors, 或者new ThreadPoolExecutor（））, 线程池可以解决什么问题（减少了线程创建销毁的时间）, 可以继续问一下一个线程被提交到线程池会做些什么判断（core线程池满?->等待队列满?->整个线程池满?->饱和策略）
线程同步问题, 一般都会回答synchronized跟lock, 他们之间的区别（lock可中断, 可超时, 可尝试获取锁）, 性能问题谁的性能更好?
问一下volatile的并语义, 是不是线程安全, 适合哪些场景
有没有看过lock的源码, lock的原理是什么?通过AQS（AbstractQueuedSynchronizer）来维护一个int, 是否了解Lock.Condition.
在java虚拟机层面上做了哪些锁优化（锁的种类）,偏向锁, 轻量级锁, 重量级锁
有没有听说过悲观锁和乐观锁, 乐观锁CAS的原理是什么, 有什么缺点（ABA问题）, 怎么解决, 还有就是两个CAS在一起能否保证线程安全（不能）

5. 是否了解计算机网络

有没有听说过七层协议, ip跟tcp分别属于哪一层.
有没有用过ping命令?ping命令用的是什么协议（icmp）, 有没有经过tcp层?除了判断可达性还有哪些实用（Traceroute）, 原理是什么
有没有听过tcp三次握手, 过程是什么, 为什么要三次握手?那四次挥手呢?
6. 问一下有没有用过spring, springmvc

spring可以为我们解决什么问题（业务解耦）, springMVC在一个请求过来是怎么找到相应的处理器（
简单回答即可, 例如所有请求都会被DispatcherServlet拦截->通过request找到handler->通过handler找到HandlerAdapter）

7. 问一些简单的mysql知识 INT（11）的11代表什么, varchar(11)中的11呢

9.
是否了解mysql的读写分离, 是否了解mysql的分库分表, 会带来什么问题（不能join）

10.
mysql事务隔离级别（读为提交, 读已提交, 可重复读, 可串行化）