---
title: Hibernate
date: 2017-12-24
tags:
- Java
- Hibernate
---

<!-- TOC -->

- [关于](#关于)
- [示例代码](#示例代码)
    - [映射文件](#映射文件)
    - [核心配置文件](#核心配置文件)
    - [测试代码](#测试代码)
- [配置文件](#配置文件)
    - [映射文件](#映射文件-1)
        - [<class>标签](#class标签)
        - [<id>标签](#id标签)
        - [<property>](#property)
    - [核心配置文件](#核心配置文件-1)
- [接口和类](#接口和类)
    - [Configuration](#configuration)
    - [SessionFactory](#sessionfactory)
    - [Session](#session)
    - [Transaction](#transaction)
    - [工具类](#工具类)

<!-- /TOC -->

# 关于

* 开源对象关系映射(ORM:Object Relational Mapping)框架, 对JDBC进行了轻量级的对象封装, 使得开发者可以使用面向对象编程思维来操纵数据库.
* 轻量级JavaEE应用持久层解决方案

# 示例代码

## 映射文件

实体类名.java
`实体类名.hbm.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-mapping PUBLIC
"-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping>
	<class name="org.luvx.domain.User" table="user">
		<id name="id" column="id">
			<generator class="native"/>
		</id>
		<property name="name" column="name"/>
		<property name="password" column="password"/>
	</class>
</hibernate-mapping>
```

## 核心配置文件

`hibernate.cfg.xml`:必须是这个文件名

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
	"-//Hibernate/Hibernate Configuration DTD 3.0//EN"
	"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
	<session-factory>
		<!-- 必须配置 5个 -->
		<property name="hibernate.connection.driver_class">com.mysql.jdbc.Driver</property>
		<property name="hibernate.connection.url">jdbc:mysql:///java</property>
		<property name="hibernate.connection.username">root</property>
		<property name="hibernate.connection.password">1121</property>
		<property name="hibernate.dialect">org.hibernate.dialect.MySQLDialect</property>
		<!-- 引入映射文件 -->
		<mapping resource="org/luvx/domain/Customer.hbm.xml"/>
	</session-factory>
</hibernate-configuration>
```

> 可选的配置参数

```
#hibernate.show_sql true
#hibernate.format_sql true
#hibernate.hbm2ddl.auto update
```

> 配置参数可以查看:hibernate-release-5.0.7.Final/project/etc/hibernate.properties


## 测试代码

```Java
@Test
public void testSave(){
	// 先加载配置文件
	Configuration config = new Configuration();
	// 默认加载src目录下的配置文件
	config.configure();
	// 建造SessionFactory对象
	SessionFactory factory = config.buildSessionFactory();
	// 开启session对象
	Session session = factory.openSession();
	// 开始事务
	Transaction tr = session.beginTransaction();
	// 编写保存代码
	User c = new User();
	c.setUsername("名称");
	// 保存客户
	session.save(c);
	// 提交事务
	tr.commit();
	// 释放资源
	session.close();
	factory.close();
}
```

# 配置文件

## 映射文件

### <class>标签

用来将类与数据库表建立映射关系

* `name`: 类的全路径
* `table`: 表名.不指定则默认表名和类名相同
* `catalog`: 数据库的名称, 通常都会在配置文件中指定数据库,所以此处不指定

### <id>标签

用来将类中的属性与表中的主键建立映射, id标签就是用来配置主键的.

* `name`: 类中属性名
* `column`: 表中的字段名.不指定则默认相同
* `length`: 字段的长度, 如果数据库已经创建好了, 那么length可以不写.否则最好指定.

### <property>

用来将类中的普通属性与表中的字段建立映射.

* `name`: 类中属性名
* `column`: 表中的字段名.不指定则默认相同
* `length`: 数据长度
* `type`: 数据类型,通常不指定,交给框架

数据类型有以下三种

1. Hibernate数据类型: `type="string"`
2. Java的数据类型: `type="java.lang.String"`
3. 数据库字段的数据类型: `<column name="name" sql-type="varchar"/>`

## 核心配置文件

核心配置文件有两种文件形式指定

`hibernate.properties`形式
`hibernate.cfg.xml`(常用)

> 前者不能再其中引入映射文件

2. 关于hibernate.cfg.xml的配置文件方式

**1. 必须配置**

* hibernate.connection.driver_class: 连接数据库驱动程序
* hibernate.connection.url: 连接数据库URL
* hibernate.connection.username: 数据库用户名
* hibernate.connection.password: 数据库密码
* hibernate.dialect: 操作数据库方言

**2. 可选配置**

* hibernate.show_sql: 显示SQL
* hibernate.format_sql: 格式化SQL
* hibernate.hbm2ddl.auto: 通过映射转成DDL语句

hibernate.hbm2ddl.auto配置可指定的值:

* create: 每次都会创建一个新的表.测试时用
* create-drop: 每次都会创建一个新的表,当执行结束之后,将创建的这个表删除.测试时用
* update: 若有表,使用原来的表.没有表,创建一个新的表.同时更新表结构.
* validate: 若有表,使用原来的表.同时校验映射文件与表中字段是否一致,不一致就会报错.

**3. 引入映射**

# 接口和类

## Configuration

* Configuration对象用于配置并且启动Hibernate.
* Hibernate应用通过该对象来获得对象-关系映射文件中的元数据, 以及动态配置Hibernate属性, 然后创建SessionFactory对象.

加载配置和映射

```Java
// properties形式
Configuration configuration = new Configuration();
config.addResource("org/luvx/domain/User.hbm.xml");
// xml形式配置
Configuration configuration = new Configuration().configure();
```

## SessionFactory

* 工厂类, 是创建Session对象的工厂类
* 由Configuration通过加载配置文件创建该对象.
* SessionFactory对象中保存了当前的数据库配置信息和所有映射关系以及预定义的SQL语句.同时, SessionFactory还负责维护Hibernate二级缓存.
* 一个SessionFactory实例对应一个数据库, 应用从该对象中获得Session实例.
* SessionFactory是线程安全的, 意味着它的一个实例可以被应用的多个线程共享.
* SessionFactory是重量级的, 意味着不能随意创建或销毁它的实例.如果只访问一个数据库, 只需要创建一个SessionFactory实例, 且在应用初始化的时候完成.
* SessionFactory需要一个较大的缓存, 用来存放预定义的SQL语句及实体的映射信息.另外可以配置一个缓存插件, 这个插件被称之为Hibernate二级缓存, 被多线程所共享

> 预定义SQL语句
* 使用Configuration类创建了SessionFactory对象时, 已经在SessionFacotry对象中缓存了一些SQL语句
* 常见的SQL语句是增删改查(通过主键来查询)
* 这样做的目的是效率更高

> 一般应用使用一个SessionFactory,最好是应用启动时就完成初始化.

## Session

* Session是在Hibernate中使用最频繁的接口.也被称之为持久化管理器.它提供了和持久化有关的操作, 比如添加, 修改, 删除, 加载和查询实体对象
* Session 是应用程序与数据库之间交互操作的一个单线程对象, 是 Hibernate 运作的中心
* Session是线程不安全的,应避免多个线程使用同一个Session实例
* 所有持久化对象必须在 session 的管理下才可以进行持久化操作
* Session 对象有一个一级缓存, 被称之为Hibernate一级缓存.每个Session实例都有自己的缓存.显式执行 flush 之前, 所有的持久化操作的数据都缓存在 session 对象处
* Session是轻量级的, 它的创建和销毁不会消耗太多的资源.应为每次客户请求分配独立的Session实例

```Java
save(obj)
delete(obj)
get(Class,id)
update(obj)
saveOrUpdate(obj) // 保存或者修改(如果没有数据, 保存数据.如果有, 修改数据)
createQuery() // HQL语句的查询的方式
```

## Transaction

事务接口

* Hibernate默认情况下事务不自动提交.需要手动提交事务
* 如果没有开启事务, 那么每个Session的操作, 都相当于一个独立的事务


```Java
commit() // 提交事务
rollback() // 回滚事务
```

## 工具类

```Java
public class HibernateUtil {
	private static final Configuration cfg;
	private static final SessionFactory factory;
	static{
		// 给常量赋值
		// 加载配置文件
		cfg = new Configuration().configure();
		// 生成factory对象
		factory = cfg.buildSessionFactory();
	}
	// 获取Session对象
	public static Session openSession(){
		return factory.openSession();
	}
}
```
