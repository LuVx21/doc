---
title: Spring IOC XML
date: 2017-11-11
tag:
- Java
- Spring
---

<!-- TOC -->

- [环境准备(Eclipse)](#环境准备eclipse)
- [关于Spring框架](#关于spring框架)
    - [什么是Spring框架](#什么是spring框架)
    - [Spring框架的特点](#spring框架的特点)
- [IOC功能(xml方式)](#ioc功能xml方式)
    - [核心容器](#核心容器)
    - [Spring中的工厂(了解)](#spring中的工厂了解)
    - [Bean管理的配置文件方式](#bean管理的配置文件方式)
- [依赖注入(DI)](#依赖注入di)
    - [属性注入](#属性注入)
    - [p名称空间的注入(了解)](#p名称空间的注入了解)
    - [SpEL注入方式(了解)](#spel注入方式了解)
    - [数组,集合(List,Set,Map),Properties等的注入](#数组集合listsetmapproperties等的注入)
- [配置文件分开管理(了解)](#配置文件分开管理了解)
- [Spring框架整合WEB](#spring框架整合web)

<!-- /TOC -->

# 环境准备(Eclipse)

1. 统一工作空间的编码,选择UTF-8
2. 把创建JSP页面的编码修改UTF-8
3. 重新配置Tomcat服务器
	* 先配置Tomcat服务器
	* 选择服务器 --> open --> 选择发布项目的目录(webapps目录)

4. SSH自己配置约束

# 关于Spring框架


## 什么是Spring框架

* Spring是于2003 年兴起的一个轻量级开源Java开发框架
* 为了解决企业应用开发的复杂性而创建的.框架的主要优势之一就是其分层架构,分层架构允许使用者选择使用哪一个组件,同时为 J2EE 应用程序开发提供集成的框架.
* Spring使用基本的JavaBean来完成以前只可能由EJB完成的事情.然而,Spring的用途不仅限于服务器端的开发.从简单性、可测试性和松耦合的角度而言,任何Java应用都可以	从Spring中受益.
* Spring的核心是控制反转(`IOC`)和面向切面(`AOP`).简单来说,Spring是一个分层的JavaSE/EEfull-stack(一站式) 轻量级开源框架.

自身组件:

* WEB层:Spring MVC
* 业务层:Bean管理:(IOC)
* 持久层:Spring的JDBC模板.ORM模板用于整合其他的持久层框架


官网(http://spring.io/)
下载地址:
	http://repo.springsource.org/libs-release-local/org/springframework/spring
	http://maven.springframework.org/release/org/springframework/spring/

Spring目录结构:

* docs:API和开发规范
* libs:jar包和源码
* schema:约束

## Spring框架的特点

* 方便解耦,简化开发
	* Spring就是一个大工厂,可以将所有对象创建和依赖关系维护,交给Spring管理
* AOP编程的支持
	* Spring提供面向切面编程,可以方便的实现对程序进行权限拦截、运行监控等功能
* 声明式事务的支持
	* 只需要通过配置就可以完成对事务的管理,而无需手动编程
* 方便程序的测试
	* Spring对Junit4支持,可以通过注解方便的测试Spring程序
* 方便集成各种优秀框架
	* Spring不排斥各种优秀的开源框架,其内部提供了对各种优秀框架(如:Struts2、Hibernate、MyBatis、Quartz等)的直接支持
* 降低JavaEE API的使用难度
	* Spring 对JavaEE开发中非常难用的一些API(JDBC、JavaMail、远程调用等),都提供了封装,使这些API应用难度大大降低

# IOC功能(xml方式)

![](./img/spring-overview.png)

* IoC(Inverse of Control):控制反转,将对象的创建权反转给Spring
* 使用IOC可以解决的程序耦合性高的问题(解耦)

## 核心容器

* Beans
* Core
* Context
* Expression Language

Spring框架也需要引入日志相关的jar包
在`spring-framework-3.0.2.RELEASE-dependencies/org.apache.commons/com.springsource.org.apache.commons.logging/1.1.1`下:

`com.springsource.org.apache.commons.logging-1.1.1.jar`

还需要引入log4j的jar包,在`spring-framework-3.0.2.RELEASE-dependencies\org.apache.log4j\com.springsource.org.apache.log4j\1.2.15`下:
`com.springsource.org.apache.log4j-1.2.15.jar`

编写Java代码,如:

* UserService.java			-- 接口
* UserServiceImpl.java		-- 具体的实现类

想把UserServiceImpl实现类的创建交给Spring框架来管理,需要创建Spring框架的配置文件,完成配置

在src目录下创建`applicationContext.xml`的配置文件,名称是可以任意的,但是一般都会使用默认名称

该配置文件的约束可以参看文件:
`spring-framework-3.2.0.RELEASE\docs\spring-framework-reference\html\xsd-config.html`

具体的约束如下:

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```
完成UserService的配置

```xml
<!-- Spring的快速入门 -->
<bean id="userService" class="com.itcast.demo1.UserServiceImpl"/>
```

编写测试程序,采用Spring框架的工厂方式来获取到UserService接口的具体实现类
```java
public void demo2(){
	// 使用Spring的工厂:
	ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
	// 通过工厂获得类:
	UserService userService = (UserService) applicationContext.getBean("userService");
	userService.sayHello();
}
```


## Spring中的工厂(了解)

**ApplicationContext**

使用ApplicationContext工厂的接口,使用该接口可以获取到具体的Bean对象
该接口下有两个具体的实现类

* ClassPathXmlApplicationContext -- 加载类路径下的Spring配置文件
* FileSystemXmlApplicationContext -- 加载本地磁盘下的Spring配置文件


**BeanFactory**

Spring框架早期的创建Bean对象的工厂接口

使用BeanFactory接口也可以获取到Bean对象

```java
public void run(){
	BeanFactory factory = new XmlBeanFactory(new ClassPathResource("applicationContext.xml"));
	UserService us = (UserService) factory.getBean("us");
	us.sayHello();
}
```

>   BeanFactory和ApplicationContext的区别

* BeanFactory				-- BeanFactory采取延迟加载,第一次getBean时才会初始化Bean
* ApplicationContext		-- 在加载applicationContext.xml时候就会创建具体的Bean对象的实例,还提供了一些其他的功能
	+ 事件传递
	+ Bean自动装配
	+ 各种不同应用层的Context实现

----------

**配置Spring框架编写XML的提示**

1. 步骤一:先复制, http://www.springframework.org/schema/beans/spring-beans.xsd
2. 步骤二:搜索XML Catalog,点击Add按钮
3. 步骤三:先选择Location的schema的约束地址
	* E:\class\2016\JavaEE28\day35_Spring框架第一天\资料\spring-framework-4.2.4.RELEASE-schema\beans\spring-beans-4.2.xsd
4. 步骤四:注意:Key type要选择:Schema location
5. 步骤五:Key把http://www.springframework.org/schema/beans/spring-beans.xsd复制上


## Bean管理的配置文件方式


**技术分析之Spring框架中<bean>标签的配置**

1. id属性和name属性的区别
	* id		-- Bean起个名字,在约束中采用ID的约束,唯一
		* 取值要求:必须以字母开始,可以使用字母、数字、连字符、下划线、句话、冒号	id:不能出现特殊字符

	* name		-- Bean起个名字,没有采用ID的约束(了解)
		* 取值要求:name:出现特殊字符.如果<bean>没有id的话 , name可以当做id使用
		* Spring框架在整合Struts1的框架的时候,Struts1的框架的访问路径是以/开头的,例如:/bookAction

2. class属性:Bean对象的全路径

3. scope属性:Bean的作用范围

* singleton:单例(默认值)
* prototype:多例,在Spring框架整合Struts2框架的时候,Action类也需要交给Spring做管理,配置把Action类配置成多例！！
* request:应用在Web项目中,每次HTTP请求都会创建一个新的Bean
* session:应用在Web项目中,同一个HTTP Session 共享一个Bean
* globalsession:应用在Web项目中,多服务器间的session

4. Bean对象的创建和销毁的两个属性配置(了解)

* 说明:Spring初始化bean或销毁bean时,有时需要作一些处理工作,因此spring可以在创建和拆卸bean的时候调用bean的两个生命周期方法

* init-method		-- 当bean被载入到容器的时候调用init-method属性指定的方法
* destroy-method	-- 当bean从容器中删除的时候调用destroy-method属性指定的方法

> 想查看destroy-method的效果,有如下条件
> scope= singleton有效
> web容器中会自动调用,但是main函数或测试用例需要手动调用(需要使用ClassPathXmlApplicationContext的close()方法)

----------

# 依赖注入(DI)

**IOC和DI的概念**

* IOC:Inverse of Control,控制反转,将对象的创建权反转给Spring

* DI:Dependency Injection,依赖注入,在Spring框架负责创建Bean对象时,动态的将依赖对象注入到Bean组件中

**DI(依赖注入)**

如果UserServiceImpl的实现类中有一个属性,那么使用Spring框架的IOC功能时,可以通过依赖注入把该属性的值传入进来

具体的配置:

```xml
<bean id="us" class="com.itheima.demo1.UserServiceImpl">
	<property name="uname" value="小风"/>
</bean>
```

----------

## 属性注入

 对于类成员变量,常用的注入方式有两种

* 构造函数注入
* 属性setter方法注入

在Spring框架中提供了前两种的属性注入的方式

1. 构造方法的注入方式,两步
	* 编写Java的类,提供构造方法
		public class Car {
			private String name;
			private double money;
			public Car(String name, double money) {
				this.name = name;
				this.money = money;
			}
			@Override
			public String toString() {
				return "Car [name=" + name + ", money=" + money + "]";
			}
		}

	* 编写配置文件
		<bean id="car" class="com.itheima.demo4.Car">
			<constructor-arg name="name" value="大奔"/>
			<constructor-arg name="money" value="100"/>
		</bean>

2. 属性的setter方法的注入方式
	* 编写Java的类,提供属性和对应的set方法即可
	* 编写配置文件

3. 如果Java类的属性是另一个Java的类,那么需要怎么来注入值呢？
	* <property name="name" rel="具体的Bean的ID或者name的值"/>
	* 例如:
		<bean id="person" class="com.itheima.demo4.Person">
			<property name="pname" value="美美"/>
			<property name="car2" ref="car2"/>
		</bean>

## p名称空间的注入(了解)

Spring的2.5版本中提供的一种注入方案

步骤一:需要先引入 p 名称空间

在schema的名称空间中加入该行:

```xml
xmlns:p="http://www.springframework.org/schema/p"
````

步骤二:使用p名称空间的语法

```
p:属性名 = ""
p:属性名-ref = ""
```

步骤三:测试

```
<bean id="person" class="com.itheima.demo4.Person" p:pname="老王" p:car2-ref="car2"/>
```

## SpEL注入方式(了解)

Spring的3.0提供了一种的一种注入方案

1. SpEL:Spring Expression Language是Spring的表达式语言,有一些自己的语法

2. 语法

`#{SpEL}`

3. 例如如下的代码

```xml
	<!-- SpEL的方式 -->
	<bean id="person" class="com.itheima.demo4.Person">
		<property name="pname" value="#{'小风'}"/>
		<property name="car2" value="#{car2}"/>
	</bean>
```

4. 还支持调用类中的属性或者方法

定义类和方法,例如

```java
public class CarInfo {
	public String getCarname(){
		return "奇瑞QQ";
	}
}
```

## 数组,集合(List,Set,Map),Properties等的注入

1. 如果是数组或者List集合,注入配置文件的方式是一样的

```xml
<bean id="collectionBean" class="com.itheima.demo5.CollectionBean">
	<property name="arrs">
		<list>
			<value>美美</value>
			<value>小风</value>
		</list>
	</property>
</bean>
```

2. 如果是Set集合,注入的配置文件方式如下:

```xml
<property name="sets">
	<set>
		<value>哈哈</value>
		<value>呵呵</value>
	</set>
</property>
```

3. 如果是Map集合,注入的配置方式如下:

```xml
<property name="map">
	<map>
		<entry key="老王2" value="38"/>
		<entry key="凤姐" value="38"/>
		<entry key="如花" value="29"/>
	</map>
</property>
```

4. 如果是properties属性文件的方式,注入的配置如下:

```xml
<property name="pro">
	<props>
		<prop key="uname">root</prop>
		<prop key="pass">123</prop>
	</props>
</property>
```

# 配置文件分开管理(了解)

在src的目录下又多创建了一个配置文件,现在是两个核心的配置文件,那么加载这两个配置文件的方式有两种:

1. 主配置文件中导入其他的配置文件:

```xml
<import resource="applicationContext2.xml"/>
```

2. 工厂创建的时候直接加载多个配置文件:

```java
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml","applicationContext2.xml");
```

# Spring框架整合WEB

创建JavaWEB项目,引入Spring的开发包.编写具体的类和方法.
环境搭建好后,启动服务器来测试项目,发送每访问一次都会加载一次配置文件,这样效率会非常非常慢！！

解决上面的问题:
将工厂创建好了以后放入到ServletContext域中.使用工厂的时候,从ServletContext中获得.

`ServletContextListener`:用来监听ServletContext对象的创建和销毁的监听器.

* 当ServletContext对象创建的时候:创建工厂, 将工厂存入到ServletContext


实现上述需求需要引入`spring-web-4.2.4.RELEASE.jar`包

配置监听器:

```xml
<!-- 配置Spring的核心监听器: -->
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<!-- 加载方式:默认只能加载WEB-INF目录下的配置文件，提供配置方式，加载src目录下 -->
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>classpath:applicationContext.xml</param-value>
</context-param>
```

从ServletContext中获得工厂:
```java
ServletContext servletContext = ServletActionContext.getServletContext();
// 需要使用WEB的工厂的方式
WebApplicationContext context = WebApplicationContextUtils.getWebApplicationContext(servletContext);
CustomerService cs = (CustomerService) context.getBean("customerService");
cs.save();
```



https://www.cnblogs.com/sunniest/p/4543271.html
