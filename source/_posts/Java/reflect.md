---
title: 反射
date:
tags:
- Java
---

<!-- TOC -->

- [关于](#关于)
- [获取类对象](#获取类对象)
- [Class](#class)
- [Constructor](#constructor)
- [Field](#field)
- [Method](#method)

<!-- /TOC -->

# 关于

根据面向对象的编程思想,万物都是对象,那么一个类也是一个对象.
将一个类看做对象,可以使用该类中的成员变量,构造方法,成员方法等的技术技术Java反射技术.

# 获取类对象

获取类对象的方式:

1. Object类的getClass()方法,`User user = new User();Class clazz = user.getClass();`
2. 数据类型的静态属性class,如:`Class clazz = User.class;`
3. Class类中的静态方法,`Class clazz = Class.forName("org.luvx.entity.User");`

> 1中只适用于对象,2,3只适用于类
> 开发中常用第三种,因为第三种是一个字符串,而不是一个具体的类名.可以配置在配置文件中

# Class

通过反射去获取构造方法:
Class类:
* 		public Constructor<T> getConstructor(Class<?>... parameterTypes)  返回一个public的构造方法
* 			参数表示的是:你要获取的构造方法的构造参数个数及数据类型的class字节码文件对象
* 		public Constructor[] getConstructors():所有公共构造方法
        public Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes) 得到一个private的构造方法
* 		public Constructor[] getDeclaredConstructors():所有构造方法

# Constructor

Constructor类:
        public void setAccessible(boolean flag):值为true则指示反射的对象在使用时应该取消Java语言访问检查
                                可以访问私有
* 		public T newInstance(Object... initargs)
*			使用此 Constructor 对象表示的构造方法来创建该构造
*			方法的声明类的新实例,并用指定的初始化参数初始化该实例

```Java
Class<?> c = Class.forName("org.luvx.entity.User");
Constructor<?> con = c.getConstructor();
Object obj = con.newInstance();
```


通过反射去获取成员变量:
Class类:
* 		public Field[] getFields()
* 		public Field[] getDeclaredFields()
* 		public Field getField(String name)
        public Field getDeclaredField(String name):可访问私有
Field类:

* 		 public Object get(Object obj)等价于对象obj间接获得成员变量
        public void set(Object obj,Object value)
        将指定对象变量上此 Field 对象表示的字段设置为指定的新值
* 		 public void setAccessible(boolean flag)

通过反射去获取成员方法:
Class类:
        public Method[] getMethods()获取自己的包括父亲的公共方法
        public Method[] getDeclaredMethods()获取自己的所有的方法
        public Method getMethod(String name,Class<?>... parameterTypes)
                反映此 Class 对象所表示的类或接口的指定公共成员方法
                第一个参数表示的方法名,第二个参数表示的是方法的参数的class类型
        public Method getDeclaredMethod(String name)
Method类:
        public void setAccessible(boolean flag)
        public Object invoke(Object obj,Object... args)
                    返回值是Object接收,第一个参数表示对象是谁,第二参数表示调用该方法的实际参数
注意:
在反射方法时,如果方法的参数是一个数组,考虑到向下兼容问题,会按照JDK1.4的语法来对待（JVM会把传递的数组参数拆开,拆开就代表参数的个数不匹配）
解决办法:防止JVM拆开你的数组
    方式一:把数组看做是一个Object对象
方式二:重新构建一个Object数组,那个参数数组作为唯一的元素存在.

# Field

# Method