---
title: Java 基础(注解, 代理)
date: 2017-10-29
tags:
- Java
- Fileter
- Web
---

<!-- TOC -->

- [注解:了解注解, 可以使用注解](#注解了解注解可以使用注解)
- [Servlet3.0](#servlet30)
    - [文件上传功能](#文件上传功能)
        - [上传注意的问题:](#上传注意的问题)
- [类加载器(了解)](#类加载器了解)
- [动态代理](#动态代理)

<!-- /TOC -->

回顾:
listener(了解)
	监听器, 监听javaweb中三个域对象
	监听对象的创建和销毁
		ServletContextListener
			在项目启动的时候加载配置文件
		ServletRequestListener
		HttpSessionListener
	监听对象的属性的变化
		ServletContextAttributeListener
		ServletRequestAttributeListener
		HttpSessionAttributeListener
	监听javabean在session中状态
		注意:让javabean自动感知在session中的状态, 不需要在配置文件配置
		HttpSessionActivationListener(钝化和活化)
		HttpSessionBindingListener(绑定和解绑)
	编写:
		1.编写监听器
		2.编写配置文件
			<listener>
				<listener-class>
			</listener>
	///////////////////////////////////
filter(★)
	过滤器, 过滤请求和响应
	filter编写步骤:
		1.编写一个类
			必须实现Filter接口
			必须重写方法
		2.编写配置文件
			注册filter
			绑定路径
	filter生命周期方法:
		doFilter(request, response, chain)
			使用步骤:
			 1.强转
			 2.编写自己的业务
			 3.放行
	url-pattern的配置:
		完全匹配
		目录匹配
		后缀名匹配
	多个filter都匹配到的时候, 执行时机是根据在xml中的filter-mapping顺序决定

	filter-mapping中的两个子标签
		servlet-name:过滤指定的servlet
		dispatcher:过滤那种方式过来的请求
			REQUEST:只过滤从浏览器发送过来的请求 (默认) 一旦显式的写出来disparcher 默认的就不起作用了
			FORWARD::只过滤请求转发过来的请求

# 注解:了解注解, 可以使用注解

案例1-模拟junit测试
需求:
	在一个类的方法中添加一个@MyTest, 在另一个类的main方法中, 就可以执行带有@MyTest的方法
技术分析:
	注解
	反射
////////////////////////////
注解:
	jdk5之后提供了一个特性, 和类 接口同级
	格式:
		@interface 注解名{}
	作用:
		编译检查
		替代配置文件
		定义注解(元注解:注解上的注解)
		分析代码(用到反射)
	////////////////////////
	java中3个注解(理解)
		@Override:声明该方法是从分类上继承过来的, 执行编译期的检查
		@SuppressWarnings:抑制警告 值有好多, 只需要知道一个 all  抑制所有的警告
		@Deprecated:声明 该方法不赞成使用
	////////////////////////
	自定义注解(理解)
		注解属性:
			注解本质就是一个接口, 接口中可以有常量和抽象方法
			抽象方法在注解中就称之为注解属性
		注解属性类型:
			基本类型
			String
			Class
			Annotation
			Enum:枚举
			以上类型对应的一维数组
		注意:
			一旦注解有属性了, 使用注解的时候必须赋值, (除非这个注解属性有默认值)

		赋值的格式:
			@注解名(属性名=属性值)
			若注解类型为数组, 且只有一个值的时候, 可以有两种写法
				方式1:
					属性名 = { 值 }
				方式2:
					属性名=属性值
			若属性名为value的时候, 且只需要为这个value属性赋值的时候, value可以省略

		元注解:(理解)
			定义在注解上的注解
				@Retention  规定注解保留到什么阶段  值为RetentionPolicy的三个枚举值
					SOURCE:只在代码中保留, 在字节码文件中就删除了
					CLASS:在代码和字节码文件中保留
					RUNTIME:所有阶段都保留

				@Target 规定注解作用在什么上面 	值为ElementType的枚举值
					TYPE:作用在类 接口 等上面
					METHOD:作用方法上面
					FIELD:作用字段上面
	/////////////////////////////////////////////////////
	步骤分析:(了解)
		1.定义一个注解 @MyTest
		2.在一个测试类 MyTestTest 上的添加几个方法
			在方法上添加@MyTest

		3.在另一个有主方法的类上添加main方法
			运行main方法的时候, 需要将带有 @MyTest注解的方法执行

/////////////////////////////
案例扩展:
	获取连接的工具类, 通过配置四个参数
步骤分析:
	1.自定义一个注解JDBCInfo
		添加元注解:
			在程序运行的时候使用  @Retention
			只能作用在方法上	  @Target
		添加注解属性
			String driverClass() default "com.mysql.jdbc.Driver";
			String url();
			String username() default "root";
			String password();
	2.在jdbcutils工具类中提供一个getConnection, 在方法上面添加一个注解 @JDBCInfo(...)
		getConnection方法需要进行的操作:获取注解上的四个属性值
			获取字节码文件
			获取该方法上的注解
			获取注解的值
	3.运行的时候可以通过getConnection获取一个连接
/////////////////////////////
案例2-完成文件上传
技术分析:
	文件上传
		servlet3.0
		commons-fileupload
		框架
////////////////////////////

# Servlet3.0

* 支持注解开发
* 没有web.xml这个文件了
* 内嵌了文件上传功能

使用:

servlet:
```
@WebServlet(urlPatterns={ "/demo2", "/demo21" }, loadOnStartup=2)
```
listener:
```
@WebListener
```
filter:
```
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


# 类加载器(了解)

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


////////////////////////////////////////////////
上午回顾:
注解:
	注释:给程序员看的
	注解:给jvm看的

	java中三个注解:
		@Override:声明该方法是从父类上继承过来的
		@SuppressWarnings:抑制警告
		@Deprecated:声明该方法不赞成使用

	自定义注解:
		注解属性
			类型:
				基本类型
				string
				class
				annotation
				enum
				以上类型的一维数组
			若有属性, 使用的时候必须有值

		元注解
			@Retention:声明注解保留到那个阶段
				SOURCE:
				CLASS:
				RUNTIME:
			@Target:声明注解作用在那个部分
				TYPE:
				METHOD:
				FILED:
	注解的格式:
		@interface 注解名{

		}