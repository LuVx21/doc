---
title: Hibernate查询方式及策略
date: 2017-12-28
tags:
- Hibernate
- Java
---

<!-- TOC -->

- [Hibernate的查询方式](#hibernate的查询方式)
- [HQL](#hql)
    - [HQL查询](#hql查询)
    - [聚合查询](#聚合查询)
    - [投影查询](#投影查询)
- [QBC](#qbc)
    - [聚合函数查询](#聚合函数查询)
    - [离线条件查询](#离线条件查询)
- [SQL查询方式](#sql查询方式)
- [多表查询](#多表查询)
- [延迟加载](#延迟加载)
- [Hibernate的查询策略](#hibernate的查询策略)
- [在set标签上配置策略](#在set标签上配置策略)
- [在man-to-one标签上配置策略](#在man-to-one标签上配置策略)

<!-- /TOC -->

# Hibernate的查询方式

1. 唯一标识OID的检索
2. 对象的导航的
3. HQL(Hibernate Query Language)
4. QBC的检索(Query By Criteria)
5. SQL检索

# HQL

* HQL(Hibernate Query Language) 是面向对象的查询语言
* 在 Hibernate 提供的各种检索方式中, HQL 是使用最广的一种检索方式

## HQL查询

```Java
session.createQuery("from User").list();
// 使用别名(因为不能使用`*`)
session.createQuery("from User u").list();
session.createQuery("select u from User u").list();

// 排序查询
// 升序
session.createQuery("from User order by id").list();
// 降序
session.createQuery("from User order by id desc").list();

// 分页查询
// setFirstResult(a): 从哪条记录开始, 如果查询是从第一条开启, 值是0
// setMaxResults(b): 每页查询的记录条数
session.createQuery("from Orders").setFirstResult(0).setMaxResults(5).list();

// 带条件的查询
// setParameter("占位符?号的位置, 默认从0开始","参数的值"); 不用考虑参数的具体类型
// 按位置绑定参数的条件查询（指定下标值, 默认从0开始）
// 按名称绑定参数的条件查询（HQL语句中的 ? 号换成 :名称 的方式）
Query query = session.createQuery("from Orders where name like ? order by lkm_id desc");
query.setFirstResult(0).setMaxResults(3);
query.setParameter(0, "%PC%");
List<Orders> list = query.list();
```

## 聚合查询

```Java
List<Number> list = session.createQuery("select count(c) from User c").list();
// List<Number> list = session.createQuery("select sum(c.cust_id) from User c").list();
Long count = list.get(0).longValue();
```

## 投影查询

不查询表的所有字段即是投影查询

两种方式:

```Java
List<Object[]> list = session.createQuery("select c.username from User c").list();
for (Object[] objects : list) {
	System.out.println(Arrays.toString(objects));
}
```
```Java
List<User> list = session.createQuery("select new User(c.username,c.password) from User c").list();
for (User user : list) {
	System.out.println(user);
}
```
* 第二种方式需要在持久化类中提供对应字段的构造方法

# QBC

Query By Criteria:按条件进行查询

```Java
List<Customer> list = session.createCriteria(Orders.class).list();

// 排序查询
// 使用addOrder()的方法来设置参数, 参数使用org.hibernate.criterion.Order对象
Criteria criteria = session.createCriteria(Orders.class);
criteria.addOrder(Order.desc("id"));
List<Orders> list = criteria.list();

// 分页查询
Criteria criteria = session.createCriteria(Orders.class);
criteria.addOrder(Order.desc("id"));
criteria.setFirstResult(0);
criteria.setMaxResults(3);
List<Orders> list = criteria.list();

// 条件查询（Criterion是查询条件的接口, Restrictions类是Hibernate提供的工具类, 使用该工具类来设置查询条件）
// 条件查询使用Criteria接口的add方法, 用来传入条件.
// 使用Restrictions的添加条件的方法, 来添加条件, 例如:
// Restrictions.eq		: 相等
// Restrictions.gt		: 大于号
// Restrictions.ge		: 大于等于
// Restrictions.lt		: 小于
// Restrictions.le		: 小于等于
// Restrictions.between	: 在之间
// Restrictions.like	: 模糊查询
// Restrictions.in		: 范围
// Restrictions.and		: 并且
// Restrictions.or		: 或者

Criteria criteria = session.createCriteria(Orders.class);
criteria.add(Restrictions.or(Restrictions.eq("id", "1"), Restrictions.gt("id", 3L)));
List<Orders> list = criteria.list();
for (Orders order : list) {
System.out.println(order);
}
```

## 聚合函数查询

使用criteria.setProjection()方法添加聚合条件
Projection的聚合函数的接口, 而Projections是Hibernate提供的工具类, 使用该工具类设置聚合函数查询

```Java
Criteria criteria = session.createCriteria(Linkman.class);
criteria.setProjection(Projections.rowCount());
List<Number> list = criteria.list();
Long count = list.get(0).longValue();
```

## 离线条件查询

离线条件查询使用的是DetachedCriteria接口进行查询, 离线条件查询对象在创建的时候, 不需要使用Session对象, 只是在查询的时候使用Session对象即可.

```Java
Session session = HibernateUtils.getCurrentSession();
Transaction tr = session.beginTransaction();

DetachedCriteria criteria = DetachedCriteria.forClass(orders.class);
criteria.add(Restrictions.eq("name", "PC"));
List<orders> list = criteria.getExecutableCriteria(session).list();
for (orders order : list) {
System.out.println(order);
}
tr.commit();
```

# SQL查询方式

```Java
Session session = HibernateUtils.getCurrentSession();
Transaction tr = session.beginTransaction();

SQLQuery sqlQuery = session.createSQLQuery("select * from User where gender = ?");
sqlQuery.setParameter(0,"男");
sqlQuery.addEntity(User.class);
List<User> list = sqlQuery.list();
System.out.println(list);
tr.commit();
```

# 多表查询

HQL的多表查询

根据查询结果的返回类可分为两种:

* 非迫切返回结果是Object[]
* 迫切连接返回的结果是对象, 把客户的信息封装到客户的对象中, 把订单的信息封装到客户的Set集合中.

```Java
List<Object[]> list = session.createQuery("from Customer c inner join c.linkmans").list();
for (Object[] objects : list) {
	System.out.println(Arrays.toString(objects));
}
```

```Java
List<Customer> list = session.createQuery("from Customer c inner join fetch c.linkmans").list();
Set<Customer> set = new HashSet<Customer>(list);
for (Customer customer : set) {
	System.out.println(customer);
}
```

> 外连接查询同上述内连接相同,

# 延迟加载

1. 延迟加载先获取到代理对象, 当真正使用到该对象中的属性的时候, 才会发送SQL语句, 是Hibernate提升性能的方式
2. 类级别的延迟加载
* Session对象的load方法默认就是延迟加载
* Customer c = session.load(Customer.class, 1L);没有发送SQL语句, 当使用该对象的属性时, 才发送SQL语句

* 使类级别的延迟加载失效
	* 在<class>标签上配置lazy=”false”
	* Hibernate.initialize(Object proxy);

3. 关联级别的延迟加载（查询某个客户, 当查看该客户下的所有联系人是是否是延迟加载）
* 默认是延迟加载
	Session session = HibernateUtils.getCurrentSession();
	Transaction tr = session.beginTransaction();
	Customer c = session.get(Customer.class, 1L);
	System.out.println("=============");
	System.out.println(c.getLinkmans().size());
	tr.commit();


# Hibernate的查询策略

1. 查询策略:使用Hibernate查询一个对象的时候, 查询其关联对象.应该如何查询.是Hibernate的一种优化手段!!!
2. Hibernate的检索策略解决的问题
* 查询的时机
	Customer c1 = (Customer) session.get(Customer.class, 1);
	System.out.println(c1.getLinkmans().size());

	* lazy属性解决查询的时机的问题, 需要配置是否采用延迟加载！！

* 查询的语句形式
	List<Customer> list = session.createQuery("from Customer").list();
	for(Customer c : list){
		System.out.println(c.getLinkmans());
	}

	* fetch属性就可以解决查询语句的形式的问题！！


# 在set标签上配置策略

1. 在<set>标签上使用fetch和lazy属性
* fetch的取值				-- 控制SQL语句生成的格式
	* select				-- 默认值.发送查询语句
	* join					-- 连接查询.发送的是一条迫切左外连接!!!配置了join.lazy就失效了
	* subselect				-- 子查询.发送一条子查询查询其关联对象.(需要使用list()方法进行测试)

* lazy的取值				-- 查找关联对象的时候是否采用延迟!
	* true					-- 默认.延迟
	* false					-- 不延迟
	* extra					-- 及其懒惰

2. set标签上的默认值是fetch="select"和lazy="true"

3. 总结:Hibernate都采用了默认值, 开发中基本上使用的都是默认值.特殊的情况.


# 在man-to-one标签上配置策略

1. 在<many-to-one>标签上使用fetch和lazy属性
* fetch的取值		-- 控制SQL的格式.
	* select		-- 默认.发送基本select语句查询
	* join			-- 发送迫切左外连接查询

* lazy的取值		-- 控制加载关联对象是否采用延迟.
	* false			-- 不采用延迟加载.
	* proxy			-- 默认值.代理.现在是否采用延迟.
		* 由另一端的<class>上的lazy确定.如果这端的class上的lazy=”true”.proxy的值就是true(延迟加载).
		* 如果class上lazy=”false”.proxy的值就是false(不采用延迟.)

2. 在<many-to-one>标签上的默认值是fetch="select"和proxy
