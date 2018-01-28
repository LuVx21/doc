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

<!-- /TOC -->

# 基本类型

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