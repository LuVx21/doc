---
title: Spring:AOP
date: 
tags:
- Spring
- AOP
---

<!-- TOC -->


<!-- /TOC -->

AOP实现的关键在于AOP框架自动创建的AOP代理,
AOP代理主要分为:
* 静态代理:编译期实现,更好的性能,AspectJ为代表(AOP框架会在编译阶段生成AOP代理类)
* 动态代理:运行期实现,Spring AOP为代表
  - JDK动态代理:通过反射来接收被代理的类,要求被代理的类必须实现一个接口,核心是InvocationHandler接口和Proxy类
  - CGLIB动态代理:目标类没有实现接口,（Code Generation Library）可以在运行时动态的生成某个类的子类

> CGLIB是通过继承的方式做的动态代理,因此如果某个类被标记为final,那么它是无法使用CGLIB做动态代理的,诸如private的方法也是不可以作为切面的

静态代理是编译阶段生成AOP代理类,也就是说生成的字节码就织入了增强后的AOP对象；
动态代理则不会修改字节码,而是在内存中临时生成一个AOP对象,这个AOP对象包含了目标对象的全部方法,并且在特定的切点做了增强处理并回调原对象的方法。

