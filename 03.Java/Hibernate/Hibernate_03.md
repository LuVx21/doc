---
title: 关系映射
date: 2017-12-27
tags:
- Hibernate
- Java
---

<!-- TOC -->

- [一对多映射](#一对多映射)
    - [Bean结构](#bean结构)
    - [映射配置](#映射配置)
- [级联](#级联)
    - [级联保存](#级联保存)
    - [级联删除](#级联删除)
    - [级联配置](#级联配置)
- [外键维护](#外键维护)
    - [cascade和inverse的区别](#cascade和inverse的区别)
- [多对多映射](#多对多映射)
    - [Bean结构](#bean结构-1)
    - [映射配置](#映射配置-1)

<!-- /TOC -->

# 一对多映射

## Bean结构

```Java
public class User {
    private Integer userid;
    private String username;
    private String password;
    // 需要实例化
    private Set<Orders> orders = new HashSet<Orders>();
}
```
```Java
public class Orders {
	private Integer orderid;
	// 不要实例化
	private User user;
}
```
## 映射配置

User.hbm.xml

```xml
    <class name="org.luvx.bean.User" table="User">
        <id name="userid" column="userid">
            <generator class="native"/>
        </id>

        <property name="username" column="username"/>
        <property name="password" column="password"/>

        <set name="orders" cascade="save-update,delete ">
            <key column="userid"></key>
            <one-to-many class="org.luvx.bean.Orders"></one-to-many>
        </set>
    </class>
```
Orders.hbm.xml

```xml
  <class name="org.luvx.bean.Orders" table="Orders">
        <id name="orderid" column="orderid">
            <generator class="native"/>
        </id>

        <many-to-one name="user" class="org.luvx.bean.User" column="userid" cascade="save-update"/>
    </class>
```

# 级联

## 级联保存

在一方和多方建立起关联后,保存一方的同时会把多方的表结构和数据保存

* 级联保存是方向性
* 使用`cascade="save-update"`


## 级联删除


依照上述bean创建表结构时,会有外键的存在
因此,在删除的时候只能从多方开始删除


* Hibernate本身能够直接从一方删除,也可能配置级联删除达到删除的目的
* `cascade="delete"`


## 级联配置

cascade属性的可配置值

* `none`: 不使用级联
* `save-update`: 级联保存或更新
* `delete`: 级联删除
* `delete-orphan`: 孤儿删除.(注意:只能应用在一对多关系)
* `all`: 除了delete-orphan的所有情况.(包含save-update delete)
* `all-delete-orphan`: 包含了delete-orphan的所有情况.(包含save-update delete delete-orphan)


> 在一对多的关系中,可以将一的一方认为是父方.将多的一方认为是子方.
> 孤儿删除:在解除了父子关系的时候.将子方记录就直接删除.

# 外键维护

双方都维护外键的时候, 会产生多余的SQL语句.
原因:session的一级缓存中的快照机制, 会让双方都更新数据库, 产生了多余的SQL语句.

解决:单方维护外键

在<set>标签上配置一个`inverse="true"`.true:放弃.false:不放弃.默认值是false


## cascade和inverse的区别

* cascade用来级联操作(保存, 修改和删除)
* inverse用来维护外键的

# 多对多映射

## Bean结构

```Java
public class Orders {

    private Integer orderid;
    // 不要实例化
    private User user;

    private Set<Product> products = new HashSet<Product>();
}
```
```Java
public class Product {

    private Integer productid;
    private String productname;

    private Set<Orders> orders=new HashSet<Orders>();
}
```
## 映射配置

Orders.hbm.xml

```xml
<class name="org.luvx.bean.Orders" table="Orders">
	<id name="orderid" column="orderid">
		<generator class="native"/>
	</id>
	<many-to-one name="user" class="org.luvx.bean.User" column="userid" cascade="save-update"/>
	<!--多对多-->
	<!--cascade和inverse不要同时配置在同一方-->
	<set name="products" table="temp_orders_products" cascade="save-update">
		<key column="temp_orderid"/>
		<many-to-many class="org.luvx.bean.Product" column="temp_productid"></many-to-many>
	</set>

```

Product.hbm.xml

```xml
</class>
	<class name="org.luvx.bean.Product" table="Product">
	<id name="productid" column="productid">
		<generator class="native"/>
	</id>
	<property name="productname" column="productname"/>
	<!--多对多-->
	<set name="orders" table="temp_orders_products" inverse="true">
		<key column="temp_productid"/>
		<many-to-many class="org.luvx.bean.Orders" column="temp_orderid"></many-to-many>
	</set>
</class>
```

> 多对多进行双向关联的时候:必须有一方去放弃外键维护权
> 多对多关系中,除了两个多方之外,还有一个第三方表存在,即`temp_orders_products`
> 级联同一对多
