---
title: Java String
date: 2017-06-02
tags:
- Java
categories:
- Java
---

<!-- TOC -->

- [String](#string)
- [StringBuffer](#stringbuffer)
- [StringBuilder](#stringbuilder)
- [summary](#summary)

<!-- /TOC -->

* String:字符串常量
* StringBuffer:字符串变量(线程安全)
* StringBuilder:字符串变量(非线程安全)

# String

底层是`private final char[] value`型数组
使用了final关键字,因此数组元素不可更改,即使只是更改String类型对象的某一个字符,实质也是产生一个新的对象

对String变量进行操作,内部都是生成了一个新的String对象.
频繁进行运算后,堆内存中会存在大量未引用的对象,这样会触发GC工作,所以本身操作加上GC,会导致jvm性能变差

操作String对象的方法中,如relplace(),return关键字后面是return new,所以是创建了新对象

适用于字符串不经常变化的场合

# StringBuffer

继承自`AbstractStringBuilder`抽象类
底层是`char[] value`型数组

操作对象是对象本身,而没有生成新的对象

在操作对象的方法中,如append(),return关键字后面是return this.所以没有创建新对象

适用于频繁进行字符串运算,并发多线程

# StringBuilder

使用方法基本上同StringBuffer,但不是线程安全的

适用于频繁进行字符串运算,单线程

# summary

通常情况下
StringBuilder > StringBuffer > String

String类的操作是产生新的String对象，
而StringBuilder和StringBuffer只是一个字符数组的扩容而已，
所以String类的操作要远慢于StringBuffer和StringBuilder。
