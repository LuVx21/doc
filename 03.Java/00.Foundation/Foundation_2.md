---
title: Java 基础(注解, 代理)
date: 2017-10-29
tags:
- Java
- Fileter
- Web
---

<!-- TOC -->

- [Servlet3.0](#servlet30)
    - [文件上传功能](#文件上传功能)
        - [上传注意的问题:](#上传注意的问题)
- [类加载器](#类加载器)
- [动态代理](#动态代理)
- [注解](#注解)

<!-- /TOC -->

# Servlet3.0

* 支持注解开发
* 没有web.xml这个文件了
* 内嵌了文件上传功能

使用:

servlet:
```Java
@WebServlet(urlPatterns={ "/demo2", "/demo21" }, loadOnStartup=2)
```
listener:
```Java
@WebListener
```
filter:
```Java
@WebFilter(urlPatterns="/*")
```

## 文件上传功能

浏览器端的要求:

* 表单的提交方式必须是`post`
* 表单必须有文件上传组件:`<input type="file" name="f">`
* form表单属性需设置为:`enctype="multipart/form-data"`

服务器获取时:

1. 添加一个`@MultipartConfig`
2. 获取普通的组件

```java
request.getParameter("name属性的值")
```
3. 获取文件上传组件

```java
// 设置编码
request, setCharacterEncoding("UTF-8");
// 获取上传组件
Part part = request.getPart("name属性的值")
//获取文件的名称
Stirng sss=part.getHeader("Content-Disposition")
//然后截取才能获取文件名称
sss.substring(sss.indexof("filename=")+10, sss.length-1);
//获取文件流
part.getInputStream();
//删除临时文件
part.delete()
```

### 上传注意的问题:

__多次上传时名字重复问题__

解决方案:

方案1:在数据库中提供两个字段,
	一个字段用来存放文件的真实名称如:1.jpg
	另一个字段用来存放文件存放路径如:g:/sdfasdf.jpg
方案2:随机名称:
	uuid
	时间戳

__文件安全问题__

重要的文件存放在`web-inf`或者`meta-inf`或者`服务器创建一个路径`

大量文件的情况下, 可以分目录存放


# 类加载器

__类加载__
	编写的.java文件, jvm会将变成.class文件.该文件要想运行, 必须加载到内存中, 然后会生成一个对象.Class对象

__类加载器层次结构__

* 引导类加载器	rt.jar
* 扩展类加载器	ext/*.jar
* 应用类加载器	自己编写类

__全盘负责委托机制__

# 动态代理

动态代理:在项目运行的时候才创建一个代理对象, 对方法进行增强(控制)

方式:

* jdk中Proxy类, 前提:实现接口
* spring中cglib, 前提:继承类

动态的在内存中创建一个代理对象:

```java
Object Proxy.newProxyInstance(ClassLoader loader, Class[] interfaces, InvocationHandler h)
```

参数说明:

> * ClassLoader:代理对象类加载器, 一般我们使用的是被代理对象的类加载器
> * Class[]:代理对象需要实现接口, 一般我们使用的是被代理对象所实现的所有接口
> * InvocationHandler:执行处理类.在这里面对方法进行加强

invocationHandler中只有一个方法

```java
Object invoke(Object proxy, Method method, Object[] args)
```

参数说明:

> * proxy:代理对象
> * method:当前执行的方法
> * args:当前方法执行的时候所需要的参数
> * 返回值:就是当前method对象执行的返回值

__使用例__

```java
Proxy.newProxyInstance(被代理对象.getClass().getClassLoader(), 被代理对象.getClass().getInterfaces(), new InvocationHandler(){
	invoke(代理对象, 当前执行的方法, 需要的参数){
	}
});
```

静态代理书写步骤:

1. 要求被装饰者和装饰者实现同一个接口或者继承同一个类
2. 在装饰者中要有被装饰者的引用
3. 对需要加强的方法进行加强
4. 对不需要加强的方法调用原来的方法

# 注解

注解的本质是一个继承自`Annotation`接口的接口

常见注解:

@Override: 声明该方法是从父类上继承过来的
@SuppressWarnings: 抑制警告
@Deprecated: 声明该方法不赞成使用


1. @Target: 声明注解作用在那个部分, SOURCE, CLASS, RUNTIME
2. @Retention: 声明注解保留到那个阶段, TYPE, METHOD, FILED
3. @Documented:
4. @Inherited:


注解的格式:

```Java
public @interface MyAnnotation {
    String value() default "";
}
```

自定义注解, 默认继承了`java.lang.annotation.Annotation`接口

注解属性

类型:
	基本类型: int,float,boolean,byte,double,char,long,short
	string
	class
	annotation
	enum
	以上类型的一维数组

若有属性, 使用的时候必须有值


public或默认(default)

