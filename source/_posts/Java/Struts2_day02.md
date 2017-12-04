---
title: Struts
date: 2017-11-11
tag:
- Java
- Struts
---

<!-- TOC -->

- [Struts中使用Servlet](#struts中使用servlet)
    - [直接使用](#直接使用)
    - [完全解耦合方式](#完全解耦合方式)
    - [原生Servlet方式](#原生servlet方式)
- [结果页面](#结果页面)
    - [结果页面的种类](#结果页面的种类)
    - [结果页面的类型](#结果页面的类型)
- [Struts数据封装](#struts数据封装)
    - [数据封装方式](#数据封装方式)
    - [把数据封装到集合中](#把数据封装到集合中)
- [拦截器](#拦截器)
- [自定义拦截器](#自定义拦截器)

<!-- /TOC -->

# Struts中使用Servlet

## 直接使用

在Action类中可以获取到Servlet一些常用API

## 完全解耦合方式

Struts中提供了`ActionContext`类, 该类提供一些方法可以使用Servlet

常用的方法

|方法|说明|
|:--|:--|
|static ActionContext getContext()|获取ActionContext对象实例|
|java.util.Map<java.lang.String, java.lang.Object> getParameters()|获取请求参数, 相当于request.getParameterMap();|
|java.util.Map<java.lang.String, java.lang.Object> getSession()|获取的代表session域的Map集合, 就相当于操作session域.|
|java.util.Map<java.lang.String, java.lang.Object> getApplication() |获取代表application域的Map集合|
|void put(java.lang.String key, java.lang.Object value)|注意:向request域中存入值.|


## 原生Servlet方式

Struts提供了`ServletActionContext`类, 该类提供了一些静态的方法

* `getRequest()`
* `getResponse()`
* `getPageContext()`
* `getServletContext()`

# 结果页面

## 结果页面的种类

**全局结果页面**

> 条件:如果`<package>`包中的一些action都返回success, 并且返回的页面都是同一个JSP页面, 这样就可以配置全局的结果页面.
> 全局结果页面针对的当前的包中的所有的Action, 但是如果局部还有结果页面, 会优先局部的.
```xml
<global-results>
	<result>/demo3/suc.jsp</result>
</global-results>
```

**局部结果页面**

action标签中使用
```xml
<result>/demo3/suc.jsp</result>
```

## 结果页面的类型

结果页面使用`<result>`标签进行配置, 包含两个属性:`name`,`type`

* name: 逻辑视图的名称
* type: 指定跳转的类型,.常见的跳转类型可以去`struts-default.xml`中查找.
	* dispatcher: 转发.type的默认值.Action--->JSP
	* redirect: 重定向. Action--->JSP
	* chain: 多个action之间跳转.从一个Action转发到另一个Action. Action---Action
	* redirectAction: 多个action之间跳转.从一个Action重定向到另一个Action. Action---Action
	* stream: 文件下载时候使用的

# Struts数据封装

## 数据封装方式

Struts中提供了两类数据封装的方式

**属性驱动方式**

此方式有2种实现方法

1. 提供对应属性的set方法进行数据的封装.

* 表单的哪些属性需要封装数据, 那么在对应的Action类中提供该属性的set方法即可.
* 表单中的数据提交, 最终找到Action类中的setXxx的方法, 最后赋值给全局变量.

> 1. Struts的框架采用的是拦截器完成数据的封装.
> 2. 这种方式不是特别好:因为属性特别多, 提供特别多的set方法, 而且还需要手动将数据存入到对象中.
> 3. 这种情况下, Action类就相当于一个JavaBean, 就没有体现出MVC的思想, Action类又封装数据, 又接收请求处理, 耦合性高.

2. 在页面上, 使用OGNL表达式进行数据封装.

* 可以直接把属性封装到某一个JavaBean的对象中.
* 在页面中定义一个JavaBean, 并且提供set方法:例如:`private User user;`
* 页面中的编写发生了变化, 需要使用OGNL的方式, 表单中的写法:`<input type="text" name="user.username">`

> 只提供一个set方法还不够, 必须还需要提供user属性的get和set方法
> 先调用get方法, 判断一下是否有user对象的实例对象, 如果没有, 调用set方法把拦截器创建的对象注入进来,

**模型驱动方式**

使用模型驱动的方式, 也可以把表单中的数据直接封装到一个JavaBean的对象中, 并且表单的写法和之前的写法没有区别
编写的页面不需要任何变化, 正常编写name属性的值

模型驱动的编写步骤:

1. 手动实例化JavaBean, 即:`private User user = new User();`
2. 必须实现`ModelDriven<T>`接口, 实现getModel()的方法, 在getModel()方法中返回user即可

## 把数据封装到集合中


因为Collection接口都会有下标值, 所有页面的写法会有一些区别:

```jsp
<!-- list -->
<input type="text" name="products[0].name" />
<!-- map -->
<input type="text" name="map['one'].name" />
```

上述对应在Action中的代码, 需要提供products的集合, 并且提供get和set方法.

# 拦截器

拦截器是AOP(Aspect-Oriented Programming)变成思想的一种体现.

拦截器:对目标Action中的某些方法进行拦截,因此不能拦截JSP
过滤器:过滤从客户端发送到服务端的请求

拦截器和过滤器的区别

1. 拦截器是基于JAVA反射机制的, 而过滤器是基于函数回调
2. 过滤器依赖于Servlet容器, 而拦截器不依赖于Servlet容器
3. 拦截器只能对Action请求起作用(Action中的方法), 而过滤器可以对几乎所有的请求起作用(CSS JSP JS)

> 拦截器 采用 `责任链模式`:在责任链模式里, 很多对象由每一个对象对其下家的引用而连接起来形成一条链, 责任链每一个节点, 都可以继续调用下一个节点, 也可以阻止流程继续执行
> 在Struts中存在多层拦截器,并组成拦截器栈,还可以自定义拦截器和拦截器栈
> 对于拦截器和拦截器栈来说,都是按顺序调用

# 自定义拦截器

![](./img2/01-拦截器.bmp)

**编写拦截器**

实现Interceptor接口的一个类, 同时实现接口中的三个方法

```Java
protected String doIntercept(ActionInvocation invocation) throws Exception {
	// 获取session对象
	User user = (User) ServletActionContext.getRequest().getSession().getAttribute("existUser");
	if(user == null){
		// 说明, 没有登录, 后面就不会执行了
		return "login";
	}
	return invocation.invoke();
}
```


**配置拦截器**

在struts.xml中配置拦截器有两种方法

```xml
<!-- 定义拦截器 第一种方式 -->
<!--
<interceptors>
	<interceptor name="DemoInterceptor" class="me.ren.interceptor.DemoInterceptor"/>
</interceptors>
-->

<!-- 第二种方式:定义拦截器栈 -->
<interceptors>
	<interceptor name="DemoInterceptor" class="me.ren.interceptor.DemoInterceptor"/>
	<!-- 定义拦截器栈 -->
	<interceptor-stack name="myStack">
		<interceptor-ref name="DemoInterceptor"/>
		<!-- 自定义拦截器栈后,需配置调用默认栈 -->
		<interceptor-ref name="defaultStack"/>
	</interceptor-stack>
</interceptors>

<action name="userAction" class="me.ren.demo3.UserAction">
	<!-- 只要是引用自己的拦截器, 默认栈的拦截器就不执行了, 必须要手动引入默认栈 -->
	<!--
	<interceptor-ref name="DemoInterceptor"/>
	<interceptor-ref name="defaultStack"/>
	-->

	<!-- 引入拦截器栈就OK -->
	<interceptor-ref name="myStack"/>
</action>
```
