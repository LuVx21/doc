---
title: Struts
date: 2017-11-11
tag:
- Java
- Struts
---

<!-- TOC -->

- [关于](#关于)
- [入门环境准备](#入门环境准备)
- [编写Action类](#编写action类)
- [编写Struts的配置文件](#编写struts的配置文件)
- [Struts2的执行流程](#struts2的执行流程)
- [配置文件](#配置文件)
    - [配置struts.xml文件提示](#配置strutsxml文件提示)
    - [配置文件加载顺序](#配置文件加载顺序)
    - [struts.xml的配置](#strutsxml的配置)
    - [常量配置](#常量配置)
    - [使用多个配置文件](#使用多个配置文件)
- [Action](#action)
    - [Action的写法](#action的写法)
    - [Action的访问方式](#action的访问方式)

<!-- /TOC -->

# 关于

* 基于MVC设计模式的Web层框架
* Struts2是Struts1的下一代产品, 是在 struts1和WebWork的技术基础上进行了合并的全新的Struts2框架.
* 其全新的Struts2的体系结构与Struts1的体系结构差别巨大.
* Struts2以WebWork为核心, 采用`拦截器`的机制来处理用户的请求, 这样的设计也使得业务逻辑控制器能够与ServletAPI完全脱离开, 所以Struts2可以理解为WebWork的更新产品.
* 虽然从Struts1到Struts2有着太大的变化, 但是相对于WebWork, Struts2的变化很小.

2. Web层框架的特点

* 都是一个特点, 前端控制器模式
* 前端控制器(核心的控制器)
* Struts2框架前端的控制器就是过滤器

> SpringMVC的前端控制器是`servlet`

# 入门环境准备

1. 创建WEB项目, 编写JSP的页面, 编写超链接, 点击超链接发送请求, 请求服务器, 让服务器的方法去执行

```jsp
<h3>Struts2的入门程序</h3>
<a href="${ pageContext.request.contextPath }/hello.action">Struts2入门程序</a>
```

2. `struts-2.3.24-all.zip`下目录结构

* apps:提供的示例应用
* libs:开发用jar包
* docs:文档
* src:源码

3. 引入需要开发的jar包

libs下的包有些是Struts项目运行的必须包,其他为组件包
可以将`struts2-blank.war`示例应用解压后,找到其使用的包

4. `web.xml`中配置前端控制器

```xml
<filter>
	<filter-name>struts2</filter-name>
	<filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>struts2</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```

# 编写Action类

Action类是动作类, 是Struts2处理请求, 封装数据, 响应页面的核心控制器.

```Java
public String sayHello(){
	System.out.println("Hello Struts2!!");
	return null;
}
```

# 编写Struts的配置文件

1. 配置文件名称是`struts.xml`(文件名必须是这个)
2. 在src下引入`struts.xml`配置文件(配置文件的路径必须是在src的目录下)
3. 配置如下

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
	"-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
	"http://struts.apache.org/dtds/struts-2.3.dtd">

<struts>
	<package name="default" namespace="/" extends="struts-default">
		<action name="hello" class="me.ren.action.HelloAction" method="sayHello">
		</action>
	</package>
</struts>
```


# Struts2的执行流程

![执行流程](https://raw.githubusercontent.com/LuVx21/hexo/master/source/_posts/Java/img/02-执行流程.bmp)

1. 执行的流程

* 编写的页面, 点击超链接, 请求提交到服务器端.
* 请求会先经过Struts2的核心过滤器(StrutsPrepareAndExecuteFilter)
* 过滤器的功能是完成了一部分代码功能
* 一系列的拦截器执行了, 进行一些处理工作.在struts-default.xml配置文件中看到有很多的拦截器.可以通过断点的方式来演示.
* 拦截器执行完后, 会根据struts.xml的配置文件找到请求路径, 找到具体的类, 通过反射的方式让方法执行.

2. 总结

`JSP页面-->StrutsPrepereAndExecuteFilter过滤器-->执行一系列拦截器(完成了部分代码)-->执行到目标Action-->返回字符串-->结果页面(result)-->页面跳转`

# 配置文件

## 配置struts.xml文件提示

1. 先找到struts.xml的配置文件, 复制http://struts.apache.org/dtds/struts-2.3.dtd
2. 选择window -- 首选项 -- 搜索xml -- 选择xml Catalog
3. 选择添加按钮(add按钮)
	* key type 要选择URI
	* key的位置把刚才复制的路径拷贝进去.
	* Location要在本地能找到struts2-2.3.dtd的真实文件.点击File System, 去磁盘中找到它
4. 如果想查看源代码
	* 选择资料/struts2/struts-2.3.24-all.zip

## 配置文件加载顺序

1. Struts2框架的核心是`StrutsPrepareAndExecuteFilter`过滤器, 该过滤器有两个功能

	* Prepare:预处理, 加载核心的配置文件
	* Execute:执行, 让部分拦截器执行

2. `StrutsPrepareAndExecuteFilter`过滤器会加载哪些配置文件呢？
通过源代码可以看到具体加载的配置文件和加载配置文件的顺序

|方法|说明|
|:---|:---|
|init_DefaultProperties();|加载org/apache/struts2/default.properties|
|init_TraditionalXmlConfigurations();|加载struts-default.xml, struts-plugin.xml, struts.xml|
|init_LegacyStrutsProperties();|加载自定义的struts.properties|
|init_CustomConfigurationProviders();|加载用户自定义配置提供器|
|init_FilterInitParameters() |加载web.xml|

3. 重点了解的配置文件

|配置文件|说明|
|:---|:---|
|default.properties|在org/apache/struts2/目录下, 代表的是配置的是Struts2的常量的值|
|struts-default.xml|在Struts2的核心包下, 代表的是Struts2核心功能的配置(Bean、拦截器、结果类型等)|
|struts.xml|重点中的重点配置, 代表WEB应用的默认配置, 在工作中, 基本就配置它就可以了(可以配置常量)|
|web.xml|配置前端控制器(可以配置常量)|

> 注意:
* 前3个配置文件是struts2框架的默认配置文件, 基本不用修改.
* 后3个配置文件可以允许自己修改struts2的常量.但后加载的配置文件修改的常量的值, 会覆盖掉前面修改的常量的值.

4. 总结(重点掌握的配置文件)

	1. 先加载default.properties文件, 在org/apache/struts2/default.properties文件, 都是常量.
	2. 又加载struts-default.xml配置文件, 在核心的jar包最下方, struts2框架的核心功能都是在该配置文件中配置的.
	3. 再加载struts.xml的配置文件, 在src的目录下, 代表用户自己配置的配置文件
	4. 最后加载web.xml的配置文件

> 哪些配置文件中可以配置常量？
* default.properties:默认值, 是不能修改的
* struts.xml:可以配置, 开发中基本上都在该配置文件中配置常量
* struts.properties:可以配置, 基本不会在该配置文件中配置
* web.xml:可以配置, 基本不会在该配置文件中配置

## struts.xml的配置

1. `<package>`标签, 如果要配置<Action>的标签, 那么必须要先配置<package>标签, 代表的包的概念

	* name:包的名称, 要求是唯一的, 管理action配置
	* extends:配置文件的继承, 可以继承其他的包, 只要继承了, 那么该包就包含了其他包的功能, 一般都是继承struts-default
	* namespace:名称空间, 一般与<action>标签中的name属性共同决定访问路径, 常见的配置如下
		* namespace="/":根名称空间
		* namespace="/aaa"	-- 带有名称的名称空间
	* abstract:抽象的.这个属性基本很少使用, 值如果是true, 那么编写的包是被继承的

2. `<action>`标签:配置action类

	* name:和<package>标签的namespace属性一起来决定访问路径的
	* class:配置Action类的全路径(默认值是ActionSupport类)
	* method:Action类中执行的方法, 如果不指定, 默认值是`execute()`

3. `<result>`标签:执行后跳转的页面

	* name:结果页面逻辑视图名称
	* type:结果类型(默认值是转发, 也可以设置其他的值)

## 常量配置

实际开发中通常在`struts.xml`中配置常量

```xml
<constant name="key" value="value"></constant>
```

常用常量:

* struts.i18n.encoding=UTF-8:指定默认编码集, 作用于HttpServletRequest的setCharacterEncoding方法
* struts.action.extension=action:该属性指定需要Struts2处理的请求后缀, 该属性的默认值是action, 即所有匹配*.action的请求都由Struts2处理.如果用户需要指定多个请求后缀, 则多个后缀之间以英文逗号(,)隔开
* struts.serve.static.browserCache=true:设置浏览器是否缓存静态内容, 默认值为true(生产环境下使用), 开发阶段最好关闭
* struts.configuration.xml.reload=false:当struts的配置文件修改后, 系统是否自动重新加载该文件, 默认值为false(生产环境下使用)
* struts.devMode=false:开发模式下使用, 这样可以打印出更详细的错误信息

## 使用多个配置文件

在`<package>`标签中, 使用`<include>`标签来引入其他的struts_xx.xml的配置文件.例如:
```xml
<struts>
	<include file="struts-part1.xml"/>
	<include file="struts-part2.xml"/>
</struts>
```

# Action

## Action的写法

**方式1:**

Action类就是一个POJO类(Plain Ordinary Java Object)

> POJO类:没有继承类也没有实现接口

**方式2:Action类可以实现Action接口**

Action接口中定义了1个方法(execute方法),5个常量

5个常量的值对应的是5个逻辑视图跳转页面(跳转的页面需要自己来配置)

* SUCCESS:成功.
* INPUT:用于数据表单校验.如果校验失败, 跳转INPUT视图.
* LOGIN:登录.
* ERROR:错误.
* NONE:页面不转向.

**方式3:继承ActionSupport类**

实际开发中使用最多的方式


## Action的访问方式

有3中常见方式

1. 通过<action>标签中的method属性, 访问到Action中的具体的方法.

传统的配置方式, 配置更清晰更好理解但是扩展需要修改配置文件等

code:
```jsp
<a href="${pageContext.request.contextPath}/addBook.action">添加</a>
```
```xml
<package name="demo2" extends="struts-default" namespace="/">
	<action name="addBook" class="me.ren.demo2.BookAction" method="add"></action>
</package>
```
```Java
public String add(){
	System.out.println("添加图书");
	return NONE;
}
```

2. 通配符的访问方式

可以简化配置文件的代码编写, 而且扩展和维护比较容易.
访问的路径和方法的名称必须要有某种联系.

code:
```jsp
<a href="${pageContext.request.contextPath}/order_add.action">添加订单</a>
<a href="${pageContext.request.contextPath}/order_delete.action">删除订单</a>
```
```xml
<action name="order_*" class="me.ren.demo2.OrderAction" method="{1}"></action>
```
```Java
public String add(){
	System.out.println("添加订单");
	return NONE;
}
public String delete(){
	System.out.println("删除订单");
	return NONE;
}
```

> 在JSP页面发送请求, http://localhost/struts2_01/order_add.action, 配置文件中的order`_*`可以匹配该请求, 
> `*`就相当于变成了add, method属性的值使用{1}来代替, {1}就表示的是第一个`*`号的位置所以method的值就等于了add, 
> 那么就找到Action类中的add方法, 那么add方法就执行了

3. 动态方法访问的方式

如果想完成动态方法访问的方式, 需要开启一个常量, 
```xml
<constant name="struts.enable.DynamicMethodInvocation" value="true"></constant>
```

> 不同的版本, 该常量的值不一定是true或者false, 需要自己来看一下.如果是false, 需要自己开启.

```jsp
<a href="${pageContext.request.contextPath}/product!add.action">添加</a>
<a href="${pageContext.request.contextPath}/product!delete.action">删除</a>
```

```xml
<action name="product" class="me.ren.demo2.ProductAction"></action>
```
