---
title: Java Tips
tags:
- Java
---
<!-- TOC -->

- [基本类型](#基本类型)
- [类初始化](#类初始化)
- [final](#final)
- [native](#native)
- [equals](#equals)
- [hashcode](#hashcode)
- [重写和重载](#重写和重载)
- [异常](#异常)
- [连接池](#连接池)

<!-- /TOC -->

# 基本类型

8种=4整型+2浮点型+1字符型+1布尔型

|类型|位数|默认|说明|
|:--|:--|:--|:--|
|byte|8|0|
|short|16|0|
|int|32|0|
|long|64|0L|
|float|32|0.0f|单精度,8位有效数字,数量级38|
|double|64|0.0d|双精度,17位有效数字,数量级308|
|char|16||单一的Unicode字符,范围:\u0000~\uffff(0~65535)|
|boolean|1|false|

# 类初始化

Java类初始化顺序:
父类静态变量——父类静态代码块——子类静态代码块——父类非静态变量——父类非静态代码块——父类构造函数——子类非静态变量——子类非静态代码块——子类构造函数

# final

类:不能被继承,因此不能和abstract一起使用
变量:变量声明时必须初始化,并且不可修改
方法:不能重写

# native

# equals

# hashcode

# 重写和重载

Overriding:重写,指重新设计从父类继承来的方法的逻辑
Overloaded:重载,指多个方法的方法名相同,但参数的类型或数量说返回值类型不同

# 异常

ClassCastException:类型转换异常
IndexOutOfBoundsException:索引越界异常
NullPointerException:空指针异常
NoSuchElementException:
UnsupportedOperationException:
IllegalArgumentException:非法参数异常

# 连接池

缓存起来的链接对象存储在连接池中,特点是共享,重复使用的.

数据库连接和关闭开销很大,为提高性能,会在线程池中维护一定量的连接对象,在收到连接数据库请求时,取出连接使用.
没有可使用的连接时,或进入排队序列等待或创建新的连接,具体取决于连接池的配置.
使用结束则放回连接池,其他请求可以继续使用.整个过程减少了创建数据库连接的时间.

应用服务器负责以下等功能:
* 创建连接对象
* 添加它们到连接池中
* 分派连接对象给请求
* 回收使用完毕的连接对象
* 重新将连接放回连接池
