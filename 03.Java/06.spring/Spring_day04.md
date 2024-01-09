---
title: Spring
date: 2017-11-11
tag:
- Java
- Spring
---

<!-- TOC -->

- [SSH整合](#ssh整合)
- [SSH整合用依赖](#ssh整合用依赖)
- [SSH整合用配置文件](#ssh整合用配置文件)
- [Spring整合Struts2](#spring整合struts2)
    - [方式1](#方式1)
    - [方式2](#方式2)
- [Spring整合Hibernate_1](#spring整合hibernate_1)
- [Spring整合Hibernate_2](#spring整合hibernate_2)
- [延迟加载](#延迟加载)

<!-- /TOC -->


# SSH整合

![](https://gitee.com/LuVx/img/raw/master/01-SSH回顾.bmp)

# SSH整合用依赖

**Struts2**

* struts-2.3.24\apps\struts2-blank\WEB-INF\lib\*.jar : Struts2需要的所有jar包
* struts2-spring-plugin-2.3.24.jar : Struts2整合Spring的插件包

**Hibernate**

* hibernate-release-5.0.7.Final\lib\required\*.jar : Hibernate需要的jar包
* slf4j-api-1.6.1.jar : 日志接口
* slf4j-log4j12-1.7.2.jar : 日志实现
* mysql-connector-java-5.1.7-bin.jar : MySQL的驱动包

**Spring**

* IOC核心包
* AOP核心包
* JDBC模板和事务核心包
* Spring整合JUnit测试包
* Spring整合Hibernate核心包
* Spring整合Struts2核心包

# SSH整合用配置文件

**Struts2**

* 在src目录下创建`struts.xml`, 用来配置Action
* 在web.xml中配置核心的过滤器

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

**Hibernate**

* 在src目录创建`hibernate.cfg.xml`配置文件
* 在JavaBean所在的包下映射的配置文件

**Spring**

* 在src目录下创建applicationContext.xml
* 在src目录下log4j.proerties
* 在web.xml配置整合WEB的监听器

```xml
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>classpath:applicationContext.xml</param-value>
</context-param>
```

# Spring整合Struts2

2. 编写CustomerAction接收请求, 在`struts.xml`中完成Action的配置

```xml
<package name="crm" extends="struts-default" namespace="/">
	<action name="customer_*" class="me.ren.web.action.CustomerAction" method="{1}">
	</action>
</package>
```

3. 在Action中获取到service

```Java
WebApplicationContextUtils.getWebApplicationContext(ServletActionContext.getServletContext());
```

> 编码繁琐,开发中不推荐使用

## 方式1

Action由Struts2来创建
导入的`struts2-spring-plugin-2.3.24.jar`包自带一个配置文件`struts-plugin.xml`, 该配置文件中有如下代码

```xml
<constant name="struts.objectFactory" value="spring" />	
```
开启一个常量, 如果该常量开启, 那么下面的常量就可以使用

```xml
struts.objectFactory.spring.autoWire = name
```

该常量是可以让Action的类来自动装配Bean对象

## 方式2

Action由Spring来创建
把具体的Action类配置文件`applicatonContext.xml`的配置文件中, 同时修改`struts.xml`

applicationContext.xml

```xml
<bean id="customerAction" class="me.ren.web.action.CustomerAction" scope="prototype">
```

`struts.xml`中把全路径修改为Spring中配置的ID值

```xml
<action name="customer_*" class="customerAction" method="{1}">
```

> Spring默认生成CustomerAction是单例的, 而Struts2是多例的.所以需要配置`scope="prototype"`
> `Action`中的`Service`此时必须手动注入
> 推荐使用的方式,即Action由Spring创建

# Spring整合Hibernate_1

带有hibernate.cfg.xml的配置文件.
强调:不能加绑定当前线程的配置

1. 编写CustomerDaoImpl的代码, 加入配置并且在CustomerServiceImpl中完成注入
2. 编写映射的配置文件, 并且在hibernate.cfg.xml的配置文件中引入映射的配置文件
3. 在applicationContext.xml的配置文件, 配置加载hibernate.cfg.xml的配置

```Java
<bean id="sessionFactory" class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
	<property name="configLocation" value="classpath:hibernate.cfg.xml"/>
</bean>
```

4. 在CustomerDaoImpl中想完成数据的添加, Spring提供了一个HibernateDaoSupport的工具类, 以后DAO都可以继承该类

```Java
public class CustomerDaoImpl extends HibernateDaoSupport implements CustomerDao {
	public void save(Customer c) {
		System.out.println("持久层...");
		this.getHibernateTemplate().save(c);
	}
}
```
```xml
<bean id="customerDao" class="me.ren.dao.CustomerDaoImpl">
	<property name="sessionFactory" ref="sessionFactory"/>
</bean>
```

5. 开启事务的配置

先配置事务管理器, 注意现在使用的是Hibernate, 所以需要使用Hibernate的事务管理器

```xml
<bean id="transactionManager" class="org.springframework.orm.hibernate5.HibernateTransactionManager">
<property name="sessionFactory" ref="sessionFactory"/>
</bean>
```

开启注解事务

```jsp
<tx:annotation-driven transaction-manager="transactionManager"/>
```

在Service类中添加事务注解

`@Transactional`

# Spring整合Hibernate_2

不带有`hibernate.cfg.xml`的配置文件

Hibernate配置文件中:

	* 数据库连接基本参数(4大参数)
	* Hibernate相关的属性
	* 连接池
	* 映射文件

先配置连接池相关的信息

```xml
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	<property name="driverClass" value="com.mysql.jdbc.Driver"/>
	<property name="jdbcUrl" value="jdbc:mysql:///xxx"/>
	<property name="user" value="root"/>
	<property name="password" value="root"/>
</bean>
```

修改 LocalSessionFactoryBean 的属性配置, 因为已经没有了hibernate.cfg.xml的配置文件, 所以需要修改该配置, 注入连接池

```xml
<bean id="transactionManager" class="org.springframework.orm.hibernate5.HibernateTransactionManager">
	<property name="dataSource" ref="dataSource"/>
</bean>
```

继续在 LocalSessionFactoryBean 中配置, 使用hibernateProperties属性继续来配置其他的属性, 值是properties属性文件

```xml
<!-- 配置其他的属性 -->
<property name="hibernateProperties">
	<props>
		<prop key="hibernate.dialect">org.hibernate.dialect.MySQLDialect</prop>
		<prop key="hibernate.show_sql">true</prop>
		<prop key="hibernate.format_sql">true</prop>
		<prop key="hibernate.hbm2ddl.auto">update</prop>
	</props>
</property>
<!-- 配置映射 -->
<property name="mappingResources">
	<list>
		<value>me/ren/domain/Customer.hbm.xml</value>
	</list>
</property>
```

# 延迟加载

使用延迟加载的时候, 在WEB层查询对象的时候程序会抛出异常

* 原因是延迟加载还没有发生SQL语句, 在业务层session对象就已经销毁了, 所以查询到的JavaBean对象已经变成了托管态对象
* 注意:一定要先删除javassist-3.11.0.GA.jar包(jar包冲突了)

解决办法非常简单, Spring提供了一个过滤器, 让session对象在WEB层就创建, 在WEB层销毁.只需要配置该过滤器即可

* 但是:要注意需要在struts2的核心过滤器之前进行配置
	
```xml
<filter>
	<filter-name>OpenSessionInViewFilter</filter-name>
	<filter-class>org.springframework.orm.hibernate5.support.OpenSessionInViewFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>OpenSessionInViewFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```