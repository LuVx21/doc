---
title: 内省
date:
tags:
- Java
---
<!-- TOC -->

- [关于](#关于)
- [获取PropertyDescriptor](#获取propertydescriptor)
- [beanutils](#beanutils)

<!-- /TOC -->

# 关于

属性: 勒种设置了setter和getter方法的成员变量

内省: 用于操作属性的机制(getter and setter methods)

# 获取PropertyDescriptor

* 方法1:
```Java
BeanInfo bi = Introspector.getBeanInfo(User.class);
PropertyDescriptor[] pd = bi.getPropertyDescriptors();
```
* 方法2:
```Java
PropertyDescriptor pdAge = new PropertyDescriptor("age", User.class);
// 得到getter方法
Method setMethod = pdAge.getReadMethod();
// 得到setter方法
Method getMethod = pdAge.getWriteMethod();
```

# beanutils

Apache所有的内省工具类包

BeanUtils的方法

```Java
//bean对象的name属性,等价于得到并调用getter方法
public static String getProperty(Object bean,String name)
//设置bean对象的name属性为value，等价于得到并调用setter方法
public static void setProperty(Object bean,String name,Object value)
//可将Map的元素放到bean中
public static void populate(Object bean,Map<String,? extends Object> properties)
```

```Java
Map<String, String> map = new HashMap<String, String>();
map.put("userName","foo");
USer user = new User();
BeanUtils.populate(user, map);
```

> BeanUtils可以进行类型的自动转换，但仅限基本类型

ConvertUtils的方法

```Java
// public static void register(Converter converter,Class clazz)//注册器，用于注册一个类型转换器
User user = new User();
ConvertUtils.register(new DateLocaleConverter().Date.class);
BeanUtils.setProperty(user,"birthday","1990-01-01");
```

Converter接口的方法

```Java
// Convert the specified input object into an output object of the specified type.
// type:目标类型  value:当前传入的值
Object convert(Class type, Object value)
```
