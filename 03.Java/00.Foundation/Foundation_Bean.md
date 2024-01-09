---
title: JavaBean
date: 2015-04-15
tags:
- Java
---
<!-- TOC -->

- [关于](#关于)
- [书写规范](#书写规范)

<!-- /TOC -->

# 关于

POJO(PLain Old Java Object)
VO(Value Object):值对象 
DO(Data Object)
DAO:data access object,数据访问对象
DTO:Data Transform Object
PO:persistant object,持久对象,对应数据库中的entity
BO:business object,业务对象 ,POJO在业务层的体现
EJB:一组JavaBean

po的规范(Persistent Object 持久化对象)

1. 公有类
2. 私有属性
3. 提供默认无参构造
4. 公有的getter与setter
5. 不能使用final修饰
6. 如果是基本类型, 就写它对应的包装类
7. 一般都要实现java.io.Serializable


#  书写规范

1. 必须是public
2. 字段都是private.符合java命名规范.
3. 必须有默认的构造方法.
4. 提供public的getter或setter方法, 即属性.
5. 一般需要实现java.io.Serializable

