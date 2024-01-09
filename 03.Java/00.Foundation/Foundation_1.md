---
title: Java 基础
date: 2017-10-29
tags:
- Java
---
<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [Java名词](#java名词)
- [关键字](#关键字)
- [方法](#方法)
- [数据类型](#数据类型)
- [类](#类)
- [重写和重载](#重写和重载)

<!-- /TOC -->
</details>

## Java名词

类实例化: 创建类的对象

## 关键字

**修饰符**

|           | 类   | 包   | 子类 | 其他包 | 其他包子类 |
| --------- | ---- | ---- | ---- | ------ | ---------- |
| private   | √    | ×    | ×    | ×      | ×          |
| default   | √    | √    | ×    | ×      | ×          |
| protected | √    | √    | √    | ×      | √          |
| public    | √    | √    | √    | √      | ×          |

> 可见简单地总结为:private是为类本身存在的, default是为包存在的, protected为继承体制存在
> 在看到protected修饰的方法时, 首先就要考虑重写的可行性

**private修饰的方法可以通过反射访问, 那么private的意义是什么**

不是为了绝对安全所设计, 约束常规使用.

## 方法

**`==`和`equals`和`hashCode`的区别**

* `==`是运算符, 比较两个对象的内存地址是否相同
* equals是Object类的方法, 作用是比较两个对象是否相同, 返回值为boolean型, 内部调用的`==`, 可以被子类重写(自定义比较规则)
* hashCode是Object类的native方法, 返回值为int型, 对象相同, 则hashCode一定相同, 反之不成立.hashcode不同, 对象肯定不同

重写规范:

* 重写equals时总要重写hashCode, 否则违反Object.hashCode的通用约定, 导致该类无法结合基于散列的集合一起正常使用, 如HashMap, HashSet和Hashtable等
* equals方法返回true, 则hashCode值也相同, 返回false, hashCode也是有可能相同, 当然, 尽可能不同能提高hash表的性能, 本质是减少了哈希碰撞的几率
* 就用于比较对象场景来说, equals方法比hashcode方法更有可靠性
* 重写equals方法时需要遵循通用约定: 自反性, 对称性, 传递性, 一致性, 非空性
* 重写时, 应先比较hashcode, 相同时再继续后续比较, 不同则可直接返回false
* 应用于hash容器的对象, 必须重写这两个方法

Set集合中, 元素无序且不可重复, 其不可重复的实现则是依靠hashCode和equals方法,
首先使用hashCode计算出元素的存储位置, 如该位置为空则直接插入, 不空则继续使用equals方法比较.

**Object的hashcode()是怎么计算的?**

hashcode是一个本地方法, 通过生成`.h`文件获得c代码的信息最终找到源码:
[源码:hashcode](http://hg.openjdk.java.net/jdk8/jdk8/hotspot/file/f2110083203d/src/share/vm/runtime/synchronizer.cpp#l555)的`get_next_hash`函数
不能认为hash的结果就是对象的内存地址, 只能认为与其有关系.


**局部变量使用前需要显式地赋值, 否则编译通过不了, 为什么这么设计**

全局变量的读写顺序具有不确定性, 在读时, 变量的初始化可能已经发生也可能未发生, 这与实际的代码顺序有关

而局部变量仅在局部使用, 在此范围内读写具有顺序性, 如果没有初始化便使用是没有意义的

而这种编译错误, 直接将可能出现的问题提前到编译阶段, 是一种设计约束, 减少犯错

## 数据类型

**a = a + b 与 a += b 的区别**

1. 执行效率
    * 前者的效率是低于后者的, 后者会在那块内存上直接更改, 实际上编译器会进行优化成为后者, 实际使用无区别
2. 类型转换
    * 如a和b类型不同, 前者编译会出错的,除非进一步强转,而后者不会,内部存在强制类型转换(例:`a+=b` -> `a = (int)((float)a + b)` )

![](https://gitee.com/LuVx/img/raw/master/type_trans.jpg)

当将一个数值范围小的类型(低精度)赋给一个数值范围大(高精度)的数值型变量, jvm在编译过程中会将此数值的类型自动提升,
如两个short类型数据a, b直接相加, 之后要强转为short,
而是用`+=`则会把后面的数值自动强制转换为前面的类型, 然后在那块内存上直接修改数值.
值得注意的是, 如果a,b被`final`修饰, 前种加法则不用强转, 而是被直接将计算的值赋给结果变量

另外, Java编译器会在编译期或者运行期
将`byte`和`short`类型的数据带符号扩展为相应的int类型数据,
将`boolean`和`char`类型数据零位扩展为相应的int类型数据.

大多数对于上述类型数据的操作, 实际上都是使用相应的 int 类型作为运算类型.

**int, char, long各占多少字节数**

分别是4, 2, 8个字节(byte)
Java采用unicode编码, 使用2个字节表示一个字符

**int与integer的区别**

int是Java基本类型, integer是int类型对应的包装类(对象类型),
基于OO编程思想, 设计出包装类, 方便处理对象类型和基本的转换等操作, 如String和基本类型的转换, 集合中存储包装类型
类似于intValue()来转换为基本类型, 反过来有自动拆装箱机制转换为包装类型

## 类

**String, StringBuffer, StringBuilder区别**

[参考](../03.Java/String.md)

**final, finally, finalize的区别**

- final: 声明类, 变量, 方法等, 使其不可被继承, 不可被修改, 不可被重写
- finally: 异常处理操作中, 遇到异常后必须要处理的操作可放置在finally代码块中
- finalize: 垃圾回收时使用, 通常不需要开发者主动显示调用

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

**静态属性和静态方法是否可以被继承?是否可以被重写?**

静态属性:
可以被继承

静态方法:
可以被继承, 但不可以被重写, 而是被隐藏

**forEach实现原理**

List实现了java.lang.Iterable接口, foreach语法最终被编译器转为了对`Iterator.next()`的调用

***值传递***

在Java中只有值传递, 不论是基本类型还是引用类型.

对于基本类型, 通常存储在栈空间中, 而引用类型, 具体的对象在堆内存, 引用在栈空间.

在传递的时候, 都是在栈空间将类型拷贝进行传递, 对基本类型的修改改变的都是拷贝的对象,

对引用对象修改时, 可能改变引用的指向(如传递的是包装类对象, 使用new等), 也可能修改原对象的值(如修改属性)

[阅读](http://www.importnew.com/29023.html)

[彻底解决Java的值传递和引用传递](https://juejin.im/post/5bce68226fb9a05ce46a0476)

**创建对象的方式**

* new关键字(实例创建表达式)
* 反射机制(Class类的newInstance方法, 使用Constructor类的newInstance方法)
* 使用Clone方法, 过程中不调用任何构造函数
* 使用反序列化等方式创建对象

## 重写和重载

Overriding:重写, 指重新设计从父类继承来的方法的逻辑
Overloaded:重载, 指多个方法的方法名相同, 但参数的类型或数量说返回值类型不同

**编译运行与解释运行的区别**

解释运行:源程序进入计算机内, 解释器一边解析成机器语言一遍执行, 效率较低.
通常脚本语言如Python, Shell, js等是解释执行

编译运行:执行器前先编译成机器语言, 在由运行环境执行, 效率较高.
类似Java等是编译执行

