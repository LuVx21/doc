---
title: JavaBean
date: 2015-04-15
tags:
- Java
---
<!-- TOC -->

- [关于](#关于)
- [书写规范](#书写规范)
- [JSP中操作JavaBean的动作元素](#jsp中操作javabean的动作元素)

<!-- /TOC -->

# 关于

POJO(PLain Old Java Object)
VO(Value Object)
DO(Data Object)
JavaBean
DAO
DTO
PO
BOBO:POJO在业务层的体现
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

# JSP中操作JavaBean的动作元素

导入JavaBean：
使用page指令的import属性,`<%@page import="java.util.*,org.luvx.domain.Person"%>`
声明JavaBean：

```
<jsp:useBean id="p" class="org.luvx.domain.Person" scope="page">
</jsp:useBean>
```
作用：按照指定的范围(scope)查找名称为id指定的对象.
                找到就找到了
                没有找到：调用默认构造方法创建对象(class), 然后以id的名字放到socpe指定的域范围中
属性：
　　　　id：名称为id的对象
　　　　class：调用默认方法创建对象(class)
　　　　scope：默认值是page(页面范围).即JavaBean的存活范围
　　　　　可选值：page request(请求) session(会话) application(应用)
**`<jsp:getProperty>`**
作用：获取JavaBean中的属性值
属性：
　　　　name：那个JavaBean.该标签之前必须使用useBean.
　　　　property:属性名称.切记：它对应的是getter方法, 与字段名称无关.
以下表示获取age的值
```
  <jsp:getProperty property="age" name="p"/>
```

**`<jsp:setProperty>`**
作用：设置JavaBean中的属性值
属性：
　　　　name：那个JavaBean.该标签之前必须使用useBean.
　　　　property：属性名称.切记：它对应的是setter方法, 与字段名称无关.
　　　　value:设置的值
以下表示设置p对象的name, 设置的值为name的值
```
  <jsp:setProperty property="name" name="p" param="name"/>
  <jsp:setProperty property="age" name="p" value="18"/>  //基本类型自动转换
```
> Tips:标签可以替换Java脚本.
