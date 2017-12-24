## Hibernate框架的第四天 ##

----------

**回顾：Hibernate框架的第三天**
	
	1. 一对多关联关系映射
		* JavaBean的编写
		* 编写映射的配置文件
		* 使用级联保存、删除、孤儿删除，使用cascade="save-update,delete,delete-orphan"
		* 放弃外键的维护的权力，使用inverse="true"
	
	2. 多对多关联关系映射
		* 保存，必须放弃外键的维护的权力
	
----------
	
**今天内容**
	
	1. Hibernate的查询方式
	2. Hibernate的查询策略
	
----------
	
### 案例一：使用Hibernate完成查询所有联系人功能 ###
	
----------
	
**需求分析**

	1. 完成所有的联系人的查询

----------

**技术分析之Hibernate框架的查询方式**
	
	1. 唯一标识OID的检索方式
		* session.get(对象.class,OID)
	2. 对象的导航的方式
	
	3. HQL的检索方式
		* Hibernate Query Language	-- Hibernate的查询语言
	
	4. QBC的检索方式
		* Query By Criteria	-- 条件查询
	
	5. SQL检索方式（了解）
		* 本地的SQL检索
	
----------
	
**技术分析之HQL的查询方式概述**
	
	1. HQL的介绍
		* HQL(Hibernate Query Language) 是面向对象的查询语言, 它和 SQL 查询语言有些相似
		* 在 Hibernate 提供的各种检索方式中, HQL 是使用最广的一种检索方式
	
	2. HQL与SQL的关系
		* HQL 查询语句是面向对象的,Hibernate负责解析HQL查询语句, 然后根据对象-关系映射文件中的映射信息, 把 HQL 查询语句翻译成相应的 SQL 语句. 
		* HQL 查询语句中的主体是域模型中的类及类的属性
		* SQL 查询语句是与关系数据库绑定在一起的. SQL查询语句中的主体是数据库表及表的字段
	
----------
	
**技术分析之HQL的查询演示**
	
	1. HQL基本的查询格式
		* 支持方法链的编程，即直接调用list()方法
		* 简单的代码如下
			* session.createQuery("from Customer").list();
	
	2. 使用别名的方式
		* 可以使用别名的方式
			* session.createQuery("from Customer c").list();
			* session.createQuery("select c from Customer c").list();
	
	3. 排序查询
		* 排序查询和SQL语句中的排序的语法是一样的
			* 升序
				* session.createQuery("from Customer order by cust_id").list();
			
			* 降序
				* session.createQuery("from Customer order by cust_id desc").list();
	
	4. 分页查询
		* Hibernate框架提供了分页的方法，咱们可以调用方法来完成分页
		* 两个方法如下
			* setFirstResult(a)		-- 从哪条记录开始，如果查询是从第一条开启，值是0
			* setMaxResults(b)		-- 每页查询的记录条数
			
		* 演示代码如下
			* List<LinkMan> list = session.createQuery("from LinkMan").setFirstResult(0).setMaxResults().list();
	
	5. 带条件的查询
		* setParameter("?号的位置，默认从0开始","参数的值"); 不用考虑参数的具体类型
		* 按位置绑定参数的条件查询（指定下标值，默认从0开始）
		* 按名称绑定参数的条件查询（HQL语句中的 ? 号换成 :名称 的方式）
		* 例如代码如下
			Query query = session.createQuery("from Linkman where lkm_name like ? order by lkm_id desc");
			query.setFirstResult(0).setMaxResults(3);
			query.setParameter(0, "%熊%");
			List<Linkman> list = query.list();
			for (Linkman linkman : list) {
				System.out.println(linkman);
			}
	
----------
	
**HQL的投影查询**
	
	1. 投影查询就是想查询某一字段的值或者某几个字段的值
	2. 投影查询的案例
		* 如果查询多个字段，例如下面这种方式
			List<Object[]> list = session.createQuery("select c.cust_name,c.cust_level from Customer c").list();
			for (Object[] objects : list) {
				System.out.println(Arrays.toString(objects));
			}
		
		* 如果查询两个字段，也可以把这两个字段封装到对象中
			* 先在持久化类中提供对应字段的构造方法
			* 使用下面这种HQL语句的方式
				List<Customer> list = session.createQuery("select new Customer(c.cust_name,c.cust_level) from Customer c").list();
				for (Customer customer : list) {
					System.out.println(customer);
				}
	
----------
	
**技术分析之聚合函数查询**
	
	1. 获取总的记录数
		Session session = HibernateUtils.getCurrentSession();
		Transaction tr = session.beginTransaction();
		List<Number> list = session.createQuery("select count(c) from Customer c").list();
		Long count = list.get(0).longValue();
		System.out.println(count);
		tr.commit();
	
	2. 获取某一列数据的和
		Session session = HibernateUtils.getCurrentSession();
		Transaction tr = session.beginTransaction();
		List<Number> list = session.createQuery("select sum(c.cust_id) from Customer c").list();
		Long count = list.get(0).longValue();
		System.out.println(count);
		tr.commit();
	
----------
	
**技术分析之QBC检索方式**
	
	0. QBC：Query By Criteria  按条件进行查询
	
	1. 简单查询，使用的是Criteria接口
		List<Customer> list = session.createCriteria(Customer.class).list();
		for (Customer customer : list) {
			System.out.println(customer);
		}
	
	2. 排序查询
		* 需要使用addOrder()的方法来设置参数，参数使用org.hibernate.criterion.Order对象
		* 具体代码如下：
			Session session = HibernateUtils.getCurrentSession();
			Transaction tr = session.beginTransaction();
			Criteria criteria = session.createCriteria(Linkman.class);
			// 设置排序
			criteria.addOrder(Order.desc("lkm_id"));
			List<Linkman> list = criteria.list();
			for (Linkman linkman : list) {
				System.out.println(linkman);
			}
			tr.commit();
	
	3. 分页查询
		* QBC的分页查询也是使用两个方法
			* setFirstResult();
			* setMaxResults();
		
		* 代码如下;
			Session session = HibernateUtils.getCurrentSession();
			Transaction tr = session.beginTransaction();
			Criteria criteria = session.createCriteria(Linkman.class);
			// 设置排序
			criteria.addOrder(Order.desc("lkm_id"));
			criteria.setFirstResult(0);
			criteria.setMaxResults(3);
			List<Linkman> list = criteria.list();
			for (Linkman linkman : list) {
				System.out.println(linkman);
			}
			tr.commit();
	
	4. 条件查询（Criterion是查询条件的接口，Restrictions类是Hibernate框架提供的工具类，使用该工具类来设置查询条件）
		* 条件查询使用Criteria接口的add方法，用来传入条件。
		* 使用Restrictions的添加条件的方法，来添加条件，例如：
			* Restrictions.eq			-- 相等
			* Restrictions.gt			-- 大于号
			* Restrictions.ge			-- 大于等于
			* Restrictions.lt			-- 小于
			* Restrictions.le			-- 小于等于
			* Restrictions.between		-- 在之间
			* Restrictions.like			-- 模糊查询
			* Restrictions.in			-- 范围
			* Restrictions.and			-- 并且
			* Restrictions.or			-- 或者
		
		* 测试代码如下
			Session session = HibernateUtils.getCurrentSession();
			Transaction tr = session.beginTransaction();
			Criteria criteria = session.createCriteria(Linkman.class);
			// 设置排序
			criteria.addOrder(Order.desc("lkm_id"));
			// 设置查询条件
			criteria.add(Restrictions.or(Restrictions.eq("lkm_gender", "男"), Restrictions.gt("lkm_id", 3L)));
			List<Linkman> list = criteria.list();
			for (Linkman linkman : list) {
				System.out.println(linkman);
			}
			tr.commit();
	
	5. 聚合函数查询（Projection的聚合函数的接口，而Projections是Hibernate提供的工具类，使用该工具类设置聚合函数查询）
		* 使用QBC的聚合函数查询，需要使用criteria.setProjection()方法
		* 具体的代码如下
			Session session = HibernateUtils.getCurrentSession();
			Transaction tr = session.beginTransaction();
			Criteria criteria = session.createCriteria(Linkman.class);
			criteria.setProjection(Projections.rowCount());
			List<Number> list = criteria.list();
			Long count = list.get(0).longValue();
			System.out.println(count);
			tr.commit();

----------

**技术分析之离线条件查询**
	
	1. 离线条件查询使用的是DetachedCriteria接口进行查询，离线条件查询对象在创建的时候，不需要使用Session对象，只是在查询的时候使用Session对象即可。
	2. 创建离线条件查询对象
		* DetachedCriteria criteria = DetachedCriteria.forClass(Linkman.class);
	
	3. 具体的代码如下
		Session session = HibernateUtils.getCurrentSession();
		Transaction tr = session.beginTransaction();
		
		DetachedCriteria criteria = DetachedCriteria.forClass(Linkman.class);
		// 设置查询条件
		criteria.add(Restrictions.eq("lkm_gender", "男"));
		// 查询数据
		List<Linkman> list = criteria.getExecutableCriteria(session).list();
		for (Linkman linkman : list) {
			System.out.println(linkman);
		}
		tr.commit();

----------
	
**技术分析之SQL查询方式（了解）**
	
	1. 基本语法
		Session session = HibernateUtils.getCurrentSession();
		Transaction tr = session.beginTransaction();
		
		SQLQuery sqlQuery = session.createSQLQuery("select * from cst_linkman where lkm_gender = ?");
		sqlQuery.setParameter(0,"男");
		sqlQuery.addEntity(Linkman.class);
		List<Linkman> list = sqlQuery.list();
		System.out.println(list);
		tr.commit();

----------
	
**技术分析之HQL多表查询**
	
	1. 多表的查询进来使用HQL语句进行查询，HQL语句和SQL语句的查询语法比较类似。
		* 内连接查询
			* 显示内连接
				* select * from customers c inner join orders o on c.cid = o.cno;
			* 隐式内连接
				* select * from customers c,orders o where c.cid = o.cno;
		
		* 外连接查询
			* 左外连接
				* select * from customers c left join orders o on c.cid = o.cno;
			* 右外连接
				* select * from customers c right join orders o on c.cid = o.cno;
	
	2. HQL的多表查询
		* 迫切和非迫切：
			* 非迫切返回结果是Object[]
			* 迫切连接返回的结果是对象，把客户的信息封装到客户的对象中，把订单的信息封装到客户的Set集合中。
	
	3. 内连接查询
		* 内连接使用 inner join ，默认返回的是Object数组
			Session session = HibernateUtils.getCurrentSession();
			Transaction tr = session.beginTransaction();
			List<Object[]> list = session.createQuery("from Customer c inner join c.linkmans").list();
			for (Object[] objects : list) {
				System.out.println(Arrays.toString(objects));
			}
			tr.commit();
		
		* 迫切内连接:inner join fetch ，返回的是实体对象
			Session session = HibernateUtils.getCurrentSession();
			Transaction tr = session.beginTransaction();
			List<Customer> list = session.createQuery("from Customer c inner join fetch c.linkmans").list();
			Set<Customer> set = new HashSet<Customer>(list);
			for (Customer customer : set) {
				System.out.println(customer);
			}
			tr.commit();
	
	4. 左外连接查询
		* 左外连接:	封装成List<Object[]>
		
		* 迫切左外连接
			Session session = HibernateUtils.getCurrentSession();
			Transaction tr = session.beginTransaction();
			List<Customer> list = session.createQuery("from Customer c left join fetch c.linkmans").list();
			Set<Customer> set = new HashSet<Customer>(list);
			for (Customer customer : set) {
				System.out.println(customer);
			}
			tr.commit();
	
----------
	
**案例一代码实现**
	
----------
	
### 案例二：对查询功能优化  ###
	
----------
	
**需求分析**
	
	1. 对Hibernate框架的查询进行优化
	
----------
	
**技术分析之延迟加载**
	
	1. 延迟加载先获取到代理对象，当真正使用到该对象中的属性的时候，才会发送SQL语句，是Hibernate框架提升性能的方式
	2. 类级别的延迟加载
		* Session对象的load方法默认就是延迟加载
		* Customer c = session.load(Customer.class, 1L);没有发送SQL语句，当使用该对象的属性时，才发送SQL语句
		
		* 使类级别的延迟加载失效
			* 在<class>标签上配置lazy=”false”
		    * Hibernate.initialize(Object proxy);
	
	3. 关联级别的延迟加载（查询某个客户，当查看该客户下的所有联系人是是否是延迟加载）
		* 默认是延迟加载
			Session session = HibernateUtils.getCurrentSession();
			Transaction tr = session.beginTransaction();
			Customer c = session.get(Customer.class, 1L);
			System.out.println("=============");
			System.out.println(c.getLinkmans().size());
			tr.commit();
	
----------
	
**技术分析之Hibernate框架的查询策略**
	
	1. 查询策略：使用Hibernate查询一个对象的时候，查询其关联对象.应该如何查询.是Hibernate的一种优化手段!!!	
	2. Hibernate框架的检索策略解决的问题
		* 查询的时机
			Customer c1 = (Customer) session.get(Customer.class, 1);
			System.out.println(c1.getLinkmans().size());
			
			* lazy属性解决查询的时机的问题，需要配置是否采用延迟加载！！
		
		* 查询的语句形式
			List<Customer> list = session.createQuery("from Customer").list();
			for(Customer c : list){
				System.out.println(c.getLinkmans());
			}
			
			* fetch属性就可以解决查询语句的形式的问题！！
	
----------
	
**技术分析之在set标签上配置策略**
	
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
	
	3. 总结：Hibernate框架都采用了默认值，开发中基本上使用的都是默认值。特殊的情况。
	
----------
	
**技术分析之在man-to-one标签上配置策略**
	
	1. 在<many-to-one>标签上使用fetch和lazy属性
		* fetch的取值		-- 控制SQL的格式.
			* select		-- 默认。发送基本select语句查询
			* join			-- 发送迫切左外连接查询
		
		* lazy的取值		-- 控制加载关联对象是否采用延迟.
			* false			-- 不采用延迟加载.
			* proxy			-- 默认值.代理.现在是否采用延迟.
				* 由另一端的<class>上的lazy确定.如果这端的class上的lazy=”true”.proxy的值就是true(延迟加载).
				* 如果class上lazy=”false”.proxy的值就是false(不采用延迟.)
	
	2. 在<many-to-one>标签上的默认值是fetch="select"和proxy
	
----------
	
**代码实现**

	