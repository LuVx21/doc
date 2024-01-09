---
title: 反射
date:
tags:
- Java
---
<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [关于](#关于)
- [获取类对象](#获取类对象)
- [Class](#class)
- [Constructor](#constructor)
- [Field](#field)
- [Method](#method)
- [原理](#原理)

<!-- /TOC -->
</details>

## 关于

根据面向对象的编程思想,万物都是对象,那么一个类也是一个对象.
将一个类抽象成对象,就可以使用该类中的成员变量,构造方法,成员方法等的技术技术Java反射技术.

## 获取类对象

获取类对象的方式:

1. Object类的getClass()方法,如:`User user = new User();Class clazz = user.getClass();`
2. 数据类型的静态属性class,如:`Class clazz = User.class;`
3. Class类中的静态方法,如:`Class clazz = Class.forName("org.luvx.entity.User");`

> 1中只适用于对象,2,3只适用于类
> 开发中常用第三种,因为第三种是一个字符串,而不是一个具体的类名.可以配置在配置文件中

## Class

通过反射去获取构造方法:

获取构造函数

```Java
// 返回一个public的构造方法
public Constructor<T> getConstructor(Class<?>... parameterTypes)
// 所有公共构造方法
public Constructor[] getConstructors()
public Constructor<T> getDeclaredConstructor(Class<?>... parameterTypes)
// 所有构造方法
public Constructor[] getDeclaredConstructors()
```

获取属性

```Java
public Field getField(String name)
// 可访问私有
public Field getDeclaredField(String name)
public Field[] getFields()
public Field[] getDeclaredFields()
```

获取方法

```Java
// 获取自己的包括父类的公共方法
public Method[] getMethods()
public Method getDeclaredMethod(String name)
// 获取自己的所有的方法
public Method[] getDeclaredMethods()
// 第一个参数表示的方法名,第二个参数表示的是方法的参数的class类型
public Method getMethod(String name,Class<?>... parameterTypes)
```

## Constructor

```Java
// 值为true则指示反射的对象在使用时应该取消Java语言访问检查,可以用于访问私有属性
public void setAccessible(boolean flag)
public T newInstance(Object... initargs)
```
如:
```Java
Class<?> c = Class.forName("org.luvx.entity.User");
Constructor<?> con = c.getConstructor();
Object obj = con.newInstance();
```

## Field

```Java
// 等价于对象obj调用getter获得成员变量
public Object get(Object obj)
// 将指定对象变量上此 Field 对象表示的字段设置为指定的新值,obj.setter(value)
public void set(Object obj,Object value)
public void setAccessible(boolean flag)
```

## Method

```Java
public void setAccessible(boolean flag)
// 返回值是Object接收,第一个参数表示对象是谁,第二参数表示调用该方法的实际参数,obj.xx(args)
public Object invoke(Object obj,Object... args)
```

> 在反射方法时,如果方法的参数是一个数组,考虑到向下兼容问题,会按照JDK1.4的语法来对待(JVM会把传递的数组参数拆开,拆开就代表参数的个数不匹配)
> 解决办法:防止JVM拆开你的数组
> 方式一:把数组看做是一个Object对象
> 方式二:重新构建一个Object数组,那个参数数组作为唯一的元素存在.


***反射能修改final的变量吗?***

能

```Java
public class Ren {
    public static void main(String[] args) throws Exception {
        Field nameField = Name.class.getDeclaredField("name");

        // 修改final变量用,非final不需要此三行
        Field modifiersField = Field.class.getDeclaredField("modifiers");
        modifiersField.setAccessible(true);
        modifiersField.setInt(nameField, nameField.getModifiers() & ~Modifier.FINAL);

        nameField.setAccessible(true);
        nameField.set(null, "newname");
        System.out.println(Name.getName());
    }
}

class Name {
    private static final String name = new String("oldname");
    public static String getName() {
        return name;
    }
}
```

## 原理

**反射的作用与实现原理**

基于Java中万物皆对象的观念, 一个类经过编译后生成的class文件加载进jvm, 都对应一个`java.lang.Class`对象, 包含了这个类的定义信息

所有就能够获取到这个类的注解, 属性及方法等信息,

既然涉及到class文件, 那就有必要了解其内容, 可阅读`jvm 字节码技术`相关内容, 此处推荐[字节码增强技术探索](https://tech.meituan.com/2019/09/05/java-bytecode-enhancement.html)

通过阅读反射部分的源码, 可以发现很多`native`方法, 通过这些方法, 从jvm内存区域(方法区内存储类信息, 推测应是此处)获取到相关信息进行后续使用

## 方法

```Java
public class C extends A implements B{}
A.class.isAssignableFrom(C.class)
B.class.isAssignableFrom(C.class)
```
判断一个类是否继承自或实现自某类, 或两个类相同
类似作用的有 `isInstance`, `instanceof`
