---
title: Java基础:注解原理
date:
tags:
- Java
---

# 注解

jdk5之后提供的一个特性, 和类,接口同级

注解主要作用:

* 编译检查
* 替代配置文件
* 定义注解(元注解:注解上的注解)
* 分析代码(用到反射)

格式:
```Java
@interface 注解名{}
```

java中3个注解:

* `@Override`:声明该方法是从父类上继承过来的, 执行编译期的检查
* `@SuppressWarnings`:抑制警告 值有好多, 只需要知道一个 `all`: 抑制所有的警告
* `@Deprecated`:声明该方法不推荐使用

## 自定义注解

注解属性:

注解本质就是一个接口, 接口中可以有常量和抽象方法,而抽象方法在注解中就称之为注解属性

注解属性类型:

* 基本类型
* String
* Class
* Annotation
* Enum
* 上述类型对应的一维数组

> 一旦注解有属性了, 使用注解的时候必须赋值, (除非这个注解属性有默认值)

赋值的格式:
```Java
@注解名(属性名=属性值)
```

若注解属性类型为数组, 且只有一个值的时候, 可以有两种写法
方式1: `属性名={ 属性值 }`
方式2: `属性名=属性值`
若属性名为value的时候, 且只需要为这个value属性赋值的时候, value可以省略

## 元注解

定义在注解上的注解
`@Documented`
注解是否将包含在JavaDoc中

`@Inherited`
是否允许子类继承该注解


`@Retention`
规定注解保留到什么阶段, 值为`RetentionPolicy`的三个枚举值

* SOURCE: 只在代码中保留, 在字节码文件中就删除了
* CLASS: 在代码和字节码文件中保留
* RUNTIME: 所有阶段都保留

`@Target`
规定注解作用在什么上面, 值为`ElementType`的枚举值

* TYPE: 作用在类 接口 等上面
* METHOD: 作用方法上面
* FIELD: 作用字段上面

```Java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface JDBCInfoAnno {

    String driverClass() default "com.mysql.jdbc.Driver";

    String url() default "jdbc:mysql://localhost:3306/test";

    String user() default "root";

    String password() default "root";
}
```

在获取数据库连接的方法上使用该注解,可以不依赖配置文件动态配置并获取数据库连接





