---
title: Spring JDBC&事务管理
date: 2017-11-11
tag:
- Java
- Spring
---

<!-- TOC -->

- [Spring AOP技术(注解方式)](#spring-aop技术注解方式)
    - [实现](#实现)
    - [通知类型](#通知类型)
- [Spring JDBC模板技术](#spring-jdbc模板技术)
    - [关于JDBC模板技术](#关于jdbc模板技术)
    - [内置连接池](#内置连接池)
        - [Spring JDBC模板类使用(普通方式)](#spring-jdbc模板类使用普通方式)
        - [Spring JDBC模板类使用(使用IOC)](#spring-jdbc模板类使用使用ioc)
    - [其他连接池](#其他连接池)
        - [DBCP连接池](#dbcp连接池)
        - [C3P0连接池](#c3p0连接池)
    - [JDBC模板的常见操作](#jdbc模板的常见操作)
- [Spring 事务管理](#spring-事务管理)
    - [事务回顾](#事务回顾)
    - [事务管理相关类和API](#事务管理相关类和api)
    - [事务管理案例准备](#事务管理案例准备)
    - [事务管理的分类](#事务管理的分类)
        - [编程式事务管理](#编程式事务管理)
        - [声明式事务管理](#声明式事务管理)
            - [声明式事务管理:XML方式](#声明式事务管理xml方式)
            - [声明式事务管理:注解方式](#声明式事务管理注解方式)

<!-- /TOC -->

# Spring AOP技术(注解方式)

## 实现

引入开发包:

4核心包+2日志包+1测试包+2aop开发包+2aspectJ开发包(共11个)

Spring配置文件:

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xmlns:aop="http://www.springframework.org/schema/aop"
		xsi:schemaLocation="
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">

</beans>
```

创建包结构

* com.itheima.demo1
	* CustomerDao			-- 接口
	* CustomerDaoImpl		-- 实现类

配置文件中配置目标类:

```xml
<bean id="customerDao" class="com.itheima.demo1.CustomerDaoImpl"/>
```

定义切面类:

切面类注解:

* @Aspect

具体实现:

```java
@Aspect
public class MyAspectAnno {
	@Before(value="execution(public void com.itheima.demo1.CustomerDaoImpl.save())")
	public void log(){
		System.out.println("记录日志...");
	}
}
```
配置文件中配置切面类:

```xml
<bean id="myAspectAnno" class="com.itheima.demo1.MyAspectAnno"/>
```

在配置文件中开启自动代理:

```xml
<aop:aspectj-autoproxy/>
```

测试:
```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class Demo1 {

	@Resource(name="customerDao")
	private CustomerDao customerDao;

	@Test
	public void run1(){
		customerDao.save();
		customerDao.update();
	}
}
```

## 通知类型

1. 通知类型

|注解|说明|
|:--|:--|
|`@Before`|前置通知|
|`@AfterReturing`|后置通知|
|`@Around`|环绕通知|
|`@After`|最终通知|
|`@AfterThrowing`|异常抛出通知|

> 注解的参数是切入点表达式

2. 配置通用的切入点

* `@Pointcut`:定义通用的切入点

自定义切入点:
```java
@Aspect
public class MyAspectAnno {
	@Before(value="MyAspectAnno.fn()")
	public void log(){
		System.out.println("记录日志...");
	}
	@Pointcut(value="execution(public void com.itheima.demo1.CustomerDaoImpl.save())")
	public void fn(){}
}
```

> 自定义切入点适用于同时对多个方法进行功能增强时,减少代码量

# Spring JDBC模板技术

## 关于JDBC模板技术

1. Spring框架中提供了很多持久层的模板类来简化编程,使用模板类编写程序会变的简单
2. 提供了JDBC模板,Spring框架提供的
	* JdbcTemplate类

3. Spring框架可以整合Hibernate框架,也提供了模板类
	* HibernateTemplate类

4. Spring自带一个JDBC连接池,同时也支持常见连接池,如`c3p0`

## 内置连接池

环境依赖:

* 核心包4个+日志包2个+测试包1个+aop1个(8个)
* MySQL数据库的驱动包
* `Spring-jdbc.jar`:Spring JDBC包
* `Spring-tx.jar`:Spring事务管理包

> 上述共有11个

### Spring JDBC模板类使用(普通方式)

```java
@Test
public void run1(){
	// 创建连接池,先使用Spring框架内置的连接池
	DriverManagerDataSource dataSource = new DriverManagerDataSource();
	dataSource.setDriverClassName("com.mysql.jdbc.Driver");
	dataSource.setUrl("jdbc:mysql:///spring_day03");
	dataSource.setUsername("root");
	dataSource.setPassword("root");
	// 创建模板类
	JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSource);
	// 完成数据的添加
	jdbcTemplate.update("insert into t_account values (null,?,?)", "测试",10000);
}
```

### Spring JDBC模板类使用(使用IOC)

* 步骤一:Spring管理内置的连接池

```xml
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
	<property name="driverClassName" value="com.mysql.jdbc.Driver"/>
	<property name="url" value="jdbc:mysql:///spring_day03"/>
	<property name="username" value="root"/>
	<property name="password" value="root"/>
</bean>
```
* 步骤二:Spring管理模板类

```xml
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
	<property name="dataSource" ref="dataSource"/>
</bean>
```

* 步骤三:编写测试程序

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class Demo2 {

	@Resource(name="jdbcTemplate")
	private JdbcTemplate jdbcTemplate;

	@Test
	public void run2(){
		jdbcTemplate.update("insert into t_account values (null,?,?)", "测试2",10000);
	}
}
```

## 其他连接池

### DBCP连接池

引入DBCP的2个jar包

* `com.springsource.org.apache.commons.dbcp-1.2.2.osgi.jar`
* `com.springsource.org.apache.commons.pool-1.5.3.jar`

编写配置文件:

```xml
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
	<property name="driverClassName" value="com.mysql.jdbc.Driver"/>
	<property name="url" value="jdbc:mysql:///spring_day03"/>
	<property name="username" value="root"/>
	<property name="password" value="root"/>
</bean>
```

### C3P0连接池

引入C3P0的jar包

* `com.springsource.com.mchange.v2.c3p0-0.9.1.2.jar`

编写配置文件:

```xml
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	<property name="driverClass" value="com.mysql.jdbc.Driver"/>
	<property name="jdbcUrl" value="jdbc:mysql:///spring_day03"/>
	<property name="user" value="root"/>
	<property name="password" value="root"/>
</bean>
```

## JDBC模板的常见操作

增删改查:

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class SpringDemo3 {
	@Resource(name="jdbcTemplate")
	private JdbcTemplate jdbcTemplate;

	@Test
	// 插入操作
	public void demo1(){
		jdbcTemplate.update("insert into account values (null,?,?)", "甲",10000d);
	}

	@Test
	// 修改操作
	public void demo2(){
		jdbcTemplate.update("update account set name=?,money =? where id = ?", "思雨",10000d,5);
	}

	@Test
	// 删除操作
	public void demo3(){
		jdbcTemplate.update("delete from account where id = ?", 5);
	}

	@Test
	// 查询一条记录
	public void demo4(){
		Account account = jdbcTemplate.queryForObject("select * from account where id = ?", new BeanMapper(), 1);
		System.out.println(account);
	}

	@Test
	// 查询所有记录
	public void demo5(){
		List<Account> list = jdbcTemplate.query("select * from t_account", new BeanMapper());
		for (Account account : list) {
			System.out.println(account);
		}
	}
}

class BeanMapper implements RowMapper<Account>{
	public Account mapRow(ResultSet rs, int arg1) throws SQLException {
		Account account = new Account();
		account.setId(rs.getInt("id"));
		account.setName(rs.getString("name"));
		account.setMoney(rs.getDouble("money"));
		return account;
	}
}
```

# Spring 事务管理


## 事务回顾

1. 事务:
指的是逻辑上一组操作,组成这个事务的各个执行单元,要么一起成功,要么一起失败！

2. 事务的特性
	* 原子性
	* 一致性
	* 隔离性
	* 持久性

3. 如果不考虑隔离性,引发安全性问题
	* 读问题:
		* 脏读:
		* 不可重复读:
		* 虚读:

	* 写问题:
		* 丢失更新:

4. 如何解决安全性问题
	* 读问题解决,设置数据库隔离级别
	* 写问题解决可以使用 悲观锁和乐观锁的方式解决


## 事务管理相关类和API

|接口|说明|
|:--|:--|
|PlatformTransactionManager|平台事务管理器.(真正管理事务的类).该接口有具体的实现类,根据不同的持久层框架,需要选择不同的实现类！|
|TransactionDefinition|事务定义信息.(事务的隔离级别,传播行为,超时,只读)|
|TransactionStatus|事务的状态|

> 上述对象之间的关系:平台事务管理器真正管理事务对象.根据事务定义的信息TransactionDefinition 进行事务管理,在管理事务中产生一些状态.将状态记录到TransactionStatus中

1.`PlatformTransactionManager`:平台事务管理器

接口的实现类:

* DataSourceTransactionManager
* HibernateTransactionManager

> 如果使用的Spring的JDBC模板或者MyBatis框架,需要选择`DataSourceTransactionManager`实现类
> 如果使用的是Hibernate的框架,需要选择`HibernateTransactionManager`实现类

接口的常用方法:

* void commit(TransactionStatus status)
* TransactionStatus getTransaction(TransactionDefinition definition)
* void rollback(TransactionStatus status)

2.`TransactionDefinition`内部实现

事务隔离级别的常量:

* static int ISOLATION_DEFAULT 					-- 采用数据库的默认隔离级别
* static int ISOLATION_READ_UNCOMMITTED
* static int ISOLATION_READ_COMMITTED
* static int ISOLATION_REPEATABLE_READ
* static int ISOLATION_SERIALIZABLE

事务的传播行为常量(不用设置,使用默认值):

* 先解释什么是事务的传播行为:解决的是业务层之间的方法调用！！

* PROPAGATION_REQUIRED(默认值)	-- A中有事务,使用A中的事务.如果没有,B就会开启一个新的事务,将A包含进来.(保证A,B在同一个事务中),默认值！！
* PROPAGATION_SUPPORTS			-- A中有事务,使用A中的事务.如果A中没有事务.那么B也不使用事务.
* PROPAGATION_MANDATORY			-- A中有事务,使用A中的事务.如果A没有事务.抛出异常.

* PROPAGATION_REQUIRES_NEW(记)-- A中有事务,将A中的事务挂起.B创建一个新的事务.(保证A,B没有在一个事务中)
* PROPAGATION_NOT_SUPPORTED		-- A中有事务,将A中的事务挂起.
* PROPAGATION_NEVER 			-- A中有事务,抛出异常.

* PROPAGATION_NESTED(记)		-- 嵌套事务.当A执行之后,就会在这个位置设置一个保存点.如果B没有问题.执行通过.如果B出现异常,运行客户根据需求回滚(选择回滚到保存点或者是最初始状态)


## 事务管理案例准备

1. 步骤一:创建WEB工程,引入需要的jar包

* IOC的6个包
* AOP的4个包
* C3P0的1个包
* MySQL的驱动包
* JDBC目标2个包
* 整合JUnit测试包

2. 步骤二:配置文件

使用c3p0连接池链接数据库:

```xml
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
	<property name="driverClass" value="com.mysql.jdbc.Driver"/>
	<property name="jdbcUrl" value="jdbc:mysql:///spring_day03"/>
	<property name="user" value="root"/>
	<property name="password" value="root"/>
</bean>
```

3. 步骤三:创建对应的包结构和类

* com.itheima.demo1
	* AccountService
	* AccountServlceImpl
	* AccountDao
	* AccountDaoImpl

4. 步骤四:引入Spring的配置文件,将类配置到Spring中
5. 步骤五:在业务层注入DAO ,在DAO中注入JDBC模板

```xml
<bean id="accountService" class="com.itheima.demo1.AccountServiceImpl">
	<property name="accountDao" ref="accountDao"/>
</bean>

<bean id="accountDao" class="com.itheima.demo1.AccountDaoImpl">
	<property name="dataSource" ref="dataSource"/>
</bean>
```

6. 步骤六:编写DAO和Service中的方法

```java
public class AccountDaoImpl extends JdbcDaoSupport implements AccountDao {
	public void outMoney(String out, double money) {
		this.getJdbcTemplate().update("update t_account set money = money = ? where name = ?", money,out);
	}
	public void inMoney(String in, double money) {
		this.getJdbcTemplate().update("update t_account set money = money + ? where name = ?", money,in);
	}
}
```

> 此处`dao`中使用继承自`JdbcDaoSupport`类,可以减少代码量:
> * 在`AccountDaoImpl`中可以不用创建`JdbcTemplate`对象,因为会从父类继承
> * 可以不用在`JdbcDaoSupport.xml`中配置JDBC模板类,因为`JdbcDaoSupport`类会根据连接池自动判断创建
> ```xml
> <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
> 	<property name="dataSource" ref="dataSource"/>
> </bean>
> ```
> 因为会根据连接池自动创建模板类对象,因此可将`AccountDaoImpl`的注入配置作一下修改:
> ```xml
> <property name="jdbcTemplate" ref="jdbcTemplate"/>
> ↓
> <property name="dataSource" ref="dataSource"/>
> ```
> 即删除一个`<bean />`,然后将注入对象由模板类改为连接池


7. 步骤七:编写测试程序.

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class Demo1 {

	@Resource(name="accountService")
	private AccountService accountService;

	@Test
	public void run1(){
		accountService.pay("甲", "乙", 1000);
	}
}
```

## 事务管理的分类

* 编程式事务管理:通过手动编写代码的方式完成事务的管理(不推荐)
* 声明式事务管理(底层采用AOP的技术):通过配置的方式完成事务的管理

### 编程式事务管理

1. Spring为了简化事务管理的代码:提供了模板类 TransactionTemplate,所以手动编程的方式来管理事务,只需要使用该模板类即可

2. 手动编程方式的具体步骤如下:

1. 步骤一:配置一个事务管理器,Spring使用PlatformTransactionManager接口来管理事务,所以需要使用到其实现类

```xml
<!-- 配置事务管理器 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"/>
</bean>
```

2. 步骤二:配置事务管理的模板

```xml
<!-- 配置事务管理的模板类 -->
<bean id="transactionTemplate" class="org.springframework.transaction.support.TransactionTemplate">
	<property name="transactionManager" ref="transactionManager"/>
</bean>
```
3. 步骤三:在需要进行事务管理的类中,注入事务管理的模板.

```xml
<bean id="accountService" class="com.itheima.demo1.AccountServiceImpl">
	<property name="accountDao" ref="accountDao"/>
	<property name="transactionTemplate" ref="transactionTemplate"/>
</bean>
```
4. 步骤四:在业务层使用模板管理事务:

```java
// 注入事务模板对象
private TransactionTemplate transactionTemplate;
public void setTransactionTemplate(TransactionTemplate transactionTemplate) {
	this.transactionTemplate = transactionTemplate;
}

public void pay(final String out, final String in, final double money) {
	transactionTemplate.execute(new TransactionCallbackWithoutResult() {
		protected void doInTransactionWithoutResult(TransactionStatus status) {
			// 扣钱
			accountDao.outMoney(out, money);
			// 模拟异常
			int a = 10/0;
			// 加钱
			accountDao.inMoney(in, money);
		}
	});
}
```

> 注入过程:
连接池 --> 事务管理器 --> 事务模板类 --> `ServiceImpl`类

### 声明式事务管理

声明式事务管理分成两种方式:

	* 基于AspectJ的XML方式(重点掌握)
	* 基于AspectJ的注解方式(重点掌握)

#### 声明式事务管理:XML方式

1. 步骤一:恢复转账开发环境

2. 步骤二:配置事务管理器

```xml
<!-- 配置事务管理器 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"/>
</bean>
```

3. 步骤三:配置事务增强
4. 步骤四:配置AOP的切面

```xml
<!-- 配置事务增强 -->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
	<tx:attributes>
		<!--
			name		:绑定事务的方法名,可以使用通配符,可以配置多个.
			propagation	:传播行为
			isolation	:隔离级别
			read-only	:是否只读
			timeout		:超时信息
			rollback-for:发生哪些异常回滚.
			no-rollback-for:发生哪些异常不回滚.
			-->
		<!-- 哪些方法加事务 -->
		<tx:method name="pay" propagation="REQUIRED"/>
	</tx:attributes>
</tx:advice>

<!-- 配置AOP切面产生代理 -->
<aop:config>
	<aop:advisor advice-ref="myAdvice" pointcut="execution(* com.itheima.demo2.AccountServiceImpl.pay(..))"/>
</aop:config>
```

> 如果是自己编写的切面了类,使用`<aop:aspect>`标签,如果是系统制作的,使用`<aop:advisor>`标签.

5. 步骤六:编写测试类

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext2.xml")
public class Demo2 {

	@Resource(name="accountService")
	private AccountService accountService;

	@Test
	public void run1(){
		accountService.pay("甲", "乙", 1000);
	}
}
```

> 注入过程:
连接池 --> 事务管理器 --> 配置AOP的切面

#### 声明式事务管理:注解方式

1. 步骤一:恢复转账的开发环境

2. 步骤二:配置事务管理器

```xml
<!-- 配置事务管理器  -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"/>
</bean>
```

3. 步骤三:开启注解事务

```xml
<!-- 开启注解事务 -->
<tx:annotation-driven transaction-manager="transactionManager"/>
```

4. 步骤四:在业务层上添加一个注解:`@Transactional`

5. 编写测试类

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext3.xml")
public class Demo3 {

	@Resource(name="accountService")
	private AccountService accountService;

	@Test
	public void run1(){
		accountService.pay("甲", "乙", 1000);
	}
}
```
