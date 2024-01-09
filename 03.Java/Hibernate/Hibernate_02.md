---
title: 持久化与事务
date: 2017-12-25
tag:
- Hibernate
- Java
---

<!-- TOC -->

- [持久化类](#持久化类)
    - [持久化类的编写规则](#持久化类的编写规则)
    - [自然主键和代理主键](#自然主键和代理主键)
    - [主键生成策略](#主键生成策略)
    - [持久化对象的状态](#持久化对象的状态)
    - [持久化对象状态的转变](#持久化对象状态的转变)
- [一级缓存](#一级缓存)
    - [Session对象的一级缓存](#session对象的一级缓存)
    - [控制Session的一级缓存](#控制session的一级缓存)
- [事务与并发](#事务与并发)
    - [事务相关概念](#事务相关概念)
    - [丢失更新](#丢失更新)
    - [绑定当前Session](#绑定当前session)
- [查询方式](#查询方式)
    - [Query查询接口](#query查询接口)
    - [Criteria查询接口](#criteria查询接口)

<!-- /TOC -->

# 持久化类

持久化类 = JavaBean + xxx.hbm.xml

## 持久化类的编写规则

1. 提供一个无参构造函数(public),层需要进行反射.
2. 提供一个标识属性, 映射数据表主键字段,唯一标识OID.数据库中通过主键.Java对象通过地址确定对象.持久化类通过唯一标识OID确定记录
3. 所有属性提供public访问控制符的 set或者get 方法
4. 标识属性应尽量使用基本数据类型的包装类型

## 自然主键和代理主键

* 自然主键:对象本身的一个属性.如Person,每个人都有一个身份证号.(唯一的)使用身份证号作为表的主键.自然主键.(开发中不会使用的方式)
* 代理主键:不是对象本身的一个属性.如Person,为每个人员单独创建一个字段.用这个字段作为主键.代理主键.(开发中推荐使用的方式)

## 主键生成策略

下记6种生成策略

1. increment:适用于short,int,long类型主键.不是使用的数据库自动增长机制.先获取表中最大主键值,在+1产生新的主键,不适合在高并发环境下使用
2. identity:适用于short,int,long类型主键.必须使用在有自动增长数据库中.采用的是数据库底层的自动增长机制.
3. sequence:适用于short,int,long类型主键.底层使用的是序列的增长方式.
4. uuid:适用于char,varchar类型的类型主键.使用随机的字符串作为主键.
5. native:本地策略.根据底层的数据库不同,自动选择适用于该种数据库的生成策略.(short,int,long)
	* 如果底层使用的MySQL数据库:相当于identity.
	* 如果底层使用Oracle数据库:相当于sequence.
6. assigned:主键的生成不用Hibernate管理了.必须手动设置主键.

## 持久化对象的状态

持久化类具有三个状态

* 瞬时态:Transient  Object, 没有持久化标识OID, 没有被纳入到Session对象的管理.
* 持久态:Persistent Object, 有持久化标识OID,已经被纳入到Session对象的管理.
* 脱管态:Detached Object, 有持久化标识OID,没有被纳入到Session对象的管理.

## 持久化对象状态的转变

1. 瞬时态
	* 获得瞬时态的对象
		* User user = new User()
	* 瞬时态→持久态
		* save()/saveOrUpdate();
	* 瞬时态→脱管态
		* user.setId(1)

2. 持久态
	* 获得持久态的对象
		* get()/load();
	* 持久态→瞬时态
		* delete();  --- 有争议, 进入特殊的状态(删除态:Hibernate中不建议使用的)
	* 持久态→脱管态
		* session的close()/evict()/clear();

3. 脱管态
	* 获得托管态对象(不建议直接获得脱管态的对象)
		* User user = new User();
		* user.setId(1);
	* 脱管态→持久态
		* update();/saveOrUpdate()/lock();
	* 脱管态→瞬时态
		* user.setId(null);

> 持久态对象有自动更新数据库的能力!!!

# 一级缓存

## Session对象的一级缓存

缓存: 其实就是一块内存空间,将数据源(数据库或者文件)中的数据存放到缓存中.再次获取的时候 ,直接从缓存中获取.可以提升程序的性能.

Hibernate框架提供了两种缓存:
* 一级缓存	-- 自带的不可卸载的.一级缓存的生命周期与session一致.为session级别的缓存.
* 二级缓存	-- 默认没有开启, 需要手动配置才可以使用的.二级缓存可以在多个session中共享数据,为sessionFactory级别的缓存.

Session对象的缓存

* Session接口中,有一系列的java的集合,这些java集合构成了Session级别的缓存(一级缓存).将对象存入到一级缓存中,session没有结束生命周期,那么对象在session中存放着
* 内存中包含Session实例 --> Session的缓存(一些集合) --> 集合中包含的是缓存对象.

Hibernate是如何做到数据发生变化时进行同步操作的呢?
如查数据后,会同时快照一份,修改后,会和快照进行对比

## 控制Session的一级缓存

```Java
Session.clear()// 清空缓存
Session.evict(Object entity)// 从一级缓存中清除指定的实体对象
Session.flush()// 刷出缓存
```

# 事务与并发

## 事务相关概念

如果不考虑隔离性就可能会引发一些读的问题

* 脏读: 一个事务读到了另一个事务未提交的数据.
* 不可重复读: 一个事务读到了另一个事务已经提交的update数据,导致多次查询结果不一致.
* 虚读: 一个事务读到了另一个事务已经提交的insert数据,导致多次查询结构不一致.

设置数据库的隔离级别

* 未提交读(`1`:Read uncommitted isolation):以上的读的问题都有可能发生.
* 已提交读(`2`:Read committed isolation):避免脏读,但是不可重复读, 虚读都有可能发生.
* 可重复读(`4`:Repeatable read isolation):避免脏读, 不可重复读.但是虚读是有可能发生.
* 串行化(`8`:Serializable isolation):以上读的情况都可以避免.


在`hibernate.cfg.xml`的配置文件中配置事务隔离级别:
`hibernate.connection.isolation = 4`

## 丢失更新

如果不考虑隔离性, 也可能产生写入数据的问题, 这一类的问题叫丢失更新的问题.
如同时对同一条数据进行写操作,先提交的事务的修改可能会被后提交的所覆盖

解决方案:

* 悲观锁: 采用的是数据库提供的一种锁机制, 如果采用做了这种机制, 在SQL语句的后面添加 for update 子句
	* 当A事务在操作该条记录时, 会把该条记录锁起来, 其他事务是不能操作这条记录的.
	* 只有当A事务提交后, 锁释放了, 其他事务才能操作该条记录

* 乐观锁: 采用版本号的机制来解决的.会给表结构添加一个字段version=0, 默认值是0,提交事务前会首先检查version,相同才会提交

使用Hibernate的解决方案:
* 悲观锁: 使用`session.get(User.class, 1,LockMode.UPGRADE);`
* 乐观锁: 1.在JavaBean中添加一个属性, 名称任意的.同时在映射配置文件中, 提供`<version name="属性名"/>`标签即可.


## 绑定当前Session

实际开发中,需要在业务层(service)中使用Connection来开启事务,dao层使用session有2种方案:
* 通过参数传递
* 把Connection绑定到ThreadLocal对象中


Hibernate提供了ThreadLocal的方式,使用如下:

hibernate.cfg.xml
```xml
<property name="hibernate.current_session_context_class">thread</property>
```
获取session
```Java
// 获取当前的Session对象.并且该Session对象不用手动关闭, 线程结束会自动关闭.
factory.getCurrentSession()
```

# 查询方式

## Query查询接口

```Java
// Query query = session.createQuery("from Customer");
// Query query = session.createQuery("from Customer where name = ?");
// query.setString(0, "李");
Query query = session.createQuery("from Customer where name = :aaa and age = :bbb");
query.setString("aaa", "李");
query.setInteger("bbb", 14);
List<Customer> list = query.list();
System.out.println(list);
```

## Criteria查询接口

非常合适条件查询的使用场合

```Java
// Criteria criteria = session.createCriteria(Customer.class);
// Criteria criteria = session.createCriteria(Customer.class);
// criteria.add(Restrictions.eq("name", "李"));
Criteria criteria = session.createCriteria(Customer.class);
criteria.add(Restrictions.eq("name", "李"));
criteria.add(Restrictions.eq("age", 14));
List<Customer> list = criteria.list();
System.out.println(list);
```