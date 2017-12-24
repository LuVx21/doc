## Hibernate框架第三天 ##

----------

**课程回顾：Hibernate第二天**
	
	1. 持久化类和一级缓存
		* 持久化类：JavaBean + 映射的配置文件
		* 持久化对象的三种状态
			* 瞬时态
			* 持久态：有自动更新数据的能力
			* 托管态
		* Session的一级缓存，快照机制	
		* 主键的生成策略
	
	2. 管理事务
		* 设置隔离级别
		* 丢失更新的问题，乐观锁：添加属性version，配置<version name="version">
		* 绑定本地的Session，事务需要service层开启，dao层需要使用Session对象
	
	3. 查询的接口
		* Query接口：HQL的查询
		* Criteria接口：QBC查询（按条件进行查询）
		
----------
	
**今天内容**
	
	1. Hibernate关联关系映射

----------

### 案例一：完成CRM的联系人的保存操作 ###

----------
	
**需求分析**
	
	1. 因为客户和联系人是一对多的关系，在有客户的情况下，完成联系人的添加保存操作
	
----------
	
**技术分析之Hibernate的关联关系映射之一对多映射（重点）**
	
	1. JavaWEB中一对多的设计及其建表原则
	
	2. 先导入SQL的建表语句
		* 创建今天的数据库：create database hibernate_day03;
		* 在资料中找到客户和联系人的SQL脚本
	
	3. 编写客户和联系人的JavaBean程序（注意一对多的编写规则）
		* 客户的JavaBean如下
			public class Customer {
				private Long cust_id;
				private String cust_name;
				private Long cust_user_id;
				private Long cust_create_id;
				private String cust_source;
				private String cust_industry;
				private String cust_level;
				private String cust_linkman;
				private String cust_phone;
				private String cust_mobile;
				
				private Set<Linkman> linkmans = new HashSet<Linkman>();

			}
		
		* 联系人的JavaBean如下
			public class Linkman {
				private Long lkm_id;
				private String lkm_name;
				private String lkm_gender;
				private String lkm_phone;
				private String lkm_mobile;
				private String lkm_email;
				private String lkm_qq;
				private String lkm_position;
				private String lkm_memo;
				
				private Customer customer;
				
			}
	
	4. 编写客户和联系人的映射配置文件（注意一对多的配置编写）
		* 客户的映射配置文件如下
			<class name="com.itheima.domain.Customer" table="cst_customer">
				<id name="cust_id" column="cust_id">
					<generator class="native"/>
				</id>
				<property name="cust_name" column="cust_name"/>
				<property name="cust_user_id" column="cust_user_id"/>
				<property name="cust_create_id" column="cust_create_id"/>
				<property name="cust_source" column="cust_source"/>
				<property name="cust_industry" column="cust_industry"/>
				<property name="cust_level" column="cust_level"/>
				<property name="cust_linkman" column="cust_linkman"/>
				<property name="cust_phone" column="cust_phone"/>
				<property name="cust_mobile" column="cust_mobile"/>
				
				<set name="linkmans">
					<key column="lkm_cust_id"/>
					<one-to-many class="com.itheima.domain.Linkman"/>
				</set>
			</class>
		
		* 联系人的映射配置文件如下
			<class name="com.itheima.domain.Linkman" table="cst_linkman">
				<id name="lkm_id" column="lkm_id">
					<generator class="native"/>
				</id>
				<property name="lkm_name" column="lkm_name"/>
				<property name="lkm_gender" column="lkm_gender"/>
				<property name="lkm_phone" column="lkm_phone"/>
				<property name="lkm_mobile" column="lkm_mobile"/>
				<property name="lkm_email" column="lkm_email"/>
				<property name="lkm_qq" column="lkm_qq"/>
				<property name="lkm_position" column="lkm_position"/>
				<property name="lkm_memo" column="lkm_memo"/>

				<many-to-one name="customer" class="com.itheima.domain.Customer" column="lkm_cust_id"/>
			</class>
	
----------
	
**技术分析之保存客户和联系人的数据**
	
	1. 进行双向关联进行数据的保存
	
----------
	
**技术分析之级联保存**
	
	1. 测试：如果现在代码只插入其中的一方的数据
		* 如果只保存其中的一方的数据，那么程序会抛出异常。
		* 如果想完成只保存一方的数据，并且把相关联的数据都保存到数据库中，那么需要配置级联！！
		
		* 级联保存是方向性
	
	2. 级联保存效果
		* 级联保存：保存一方同时可以把关联的对象也保存到数据库中！！
		* 使用cascade="save-update"
	
----------
	
**技术分析之级联删除**
	
	1. 先来给大家在数据库中演示含有外键的删除客户功能，那么SQL语句是会报出错误的
		* 例如：delete from customers where cid = 1;
	
	2. 如果使用Hibernate框架直接删除客户的时候，测试发现是可以删除的
	
	3. 上述的删除是普通的删除，那么也可以使用级联删除，注意：级联删除也是有方向性的！！
		* <many-to-one cascade="delete" />
	
----------
	
**技术分析之级联的取值（cascade的取值）和孤儿删除**
	
	1. 需要大家掌握的取值如下
		* none						-- 不使用级联
		* save-update				-- 级联保存或更新
		* delete					-- 级联删除
		* delete-orphan				-- 孤儿删除.(注意：只能应用在一对多关系)
		* all						-- 除了delete-orphan的所有情况.（包含save-update delete）
		* all-delete-orphan			-- 包含了delete-orphan的所有情况.（包含save-update delete delete-orphan）
	
	2. 孤儿删除（孤子删除），只有在一对多的环境下才有孤儿删除
		* 在一对多的关系中,可以将一的一方认为是父方.将多的一方认为是子方.孤儿删除:在解除了父子关系的时候.将子方记录就直接删除。
		* <many-to-one cascade="delete-orphan" />

----------

**技术分析之让某一方放弃外键的维护，为多对多做准备**
	
	1. 先测试双方都维护外键的时候，会产生多余的SQL语句。
		* 想修改客户和联系人的关系，进行双向关联，双方都会维护外键，会产生多余的SQL语句。
		
		* 产生的原因：session的一级缓存中的快照机制，会让双方都更新数据库，产生了多余的SQL语句。
	
	2. 如果不想产生多余的SQL语句，那么需要一方来放弃外键的维护！
		* 在<set>标签上配置一个inverse=”true”.true:放弃.false:不放弃.默认值是false
		* <inverse="true">

----------

**技术分析之cascade和inverse的区别**

	1. cascade用来级联操作（保存、修改和删除）
	2. inverse用来维护外键的

----------

### Hibernate的关联关系映射之多对多映射 ###

----------

**技术分析之多对多的建表原则**

	1. JavaWEB的多对多

----------

**技术分析之多对多JavaBean的编写**
	
	1. 编写用户和角色的JavaBean
		* 用户的JavaBean代码如下
			public class User {
				private Long user_id;
				private String user_code;
				private String user_name;
				private String user_password;
				private String user_state;
				
				private Set<Role> roles = new HashSet<Role>();
			}
		
		* 角色的JavaBean代码如下
			public class Role {
				private Long role_id;
				private String role_name;
				private String role_memo;
				
				private Set<User> users = new HashSet<User>();
			}
	
	2. 用户和角色的映射配置文件如下
		* 用户的映射配置文件如下
			<class name="com.itheima.domain.User" table="sys_user">
				<id name="user_id" column="user_id">
					<generator class="native"/>
				</id>
				<property name="user_code" column="user_code"/>
				<property name="user_name" column="user_name"/>
				<property name="user_password" column="user_password"/>
				<property name="user_state" column="user_state"/>
				
				<set name="roles" table="sys_user_role">
					<key column="user_id"/>
					<many-to-many class="com.itheima.domain.Role" column="role_id"/>
				</set>
			</class>
		
		* 角色的映射配置文件如下
			<class name="com.itheima.domain.Role" table="sys_role">
				<id name="role_id" column="role_id">
					<generator class="native"/>
				</id>
				<property name="role_name" column="role_name"/>
				<property name="role_memo" column="role_memo"/>
				
				<set name="users" table="sys_user_role">
					<key column="role_id"/>
					<many-to-many class="com.itheima.domain.User" column="user_id"/>
				</set>
			</class>
	
	3. 多对多进行双向关联的时候:必须有一方去放弃外键维护权
	
----------
	
**技术分析之多对多的级联保存**
	
	1. 级联保存
		* <set cascade="save-update">
	
----------
	
**级联删除（在多对多中是很少使用的）**
	
	1. 级联删除