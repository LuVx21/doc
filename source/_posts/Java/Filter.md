---
title: Java Fileter
date: 2017-10-29
tags:
- Java
- Fileter
- Web
---
<!-- TOC -->

- [关于](#关于)
- [使用](#使用)

<!-- /TOC -->
# 关于

filter:过滤器
	过滤请求和响应
	作用:
		自动登录.
		统一编码.
		过滤关键字
		....
	Filter是一个接口

# 使用

编写filter步骤:
	1.编写一个类
		a.实现filter接口
		b.重写方法
	2.编写配置文件
		a.注册filter
		b.绑定路径
	3.测试

Filter接口的方法:
	init(FilterConfig config):初始化操作
	doFilter(ServletRequest request, ServletResponse response, FilterChain chain):处理业务逻辑
	destroy() :销毁操作
filter的生命周期(了解)
	filter单实例多线程
	filter在服务器启动的时候 服务器创建filter 调用init方法 实现初始化操作
	请求来的时候, 创建一个线程 根据路径调用dofilter 执行业务逻辑
	当filter被移除的时候或者服务器正常关闭的时候 调用destory方法 执行销毁操作.
FilterChain:过滤链
	通过chain的dofilter方法, 可以将请求放行到下一个过滤器, 直到最后一个过滤器放行才可以访问到servlet|jsp
	doFilter()放行方法
★url-pattern配置
	3种
	完全匹配	必须以"/" 开始  例如: /a/b
	目录匹配	必须以"/" 开始 以"*"结束  例如:/a/b/*
	后缀名匹配	以"*."开始 以字符结束   例如 :  *.jsp  *.do  *.action
例如:
	afilter  路径  /*
	bFilter  路径  /demo4
★一个资源有可能被多个过滤器匹配成功, 多个过滤器的执行顺序是按照web.xml中filter-mapping的顺序执行的

///////////////////////////////
步骤分析:
	1.数据库和表
		create database day16;
		use day16;
		create table user(
			id int primary key auto_increment, 
			username varchar(20), 
			password varchar(20)
		);
		insert into user values(null, 'tom', '123');
	2.web项目
		jar包 工具类 配置文件
	3.新建一个登录页面 表单
	4.表单提交 loginservlet
		接受用户名和密码
		调用service完成登录操作, 返回值User
		判断user是否为空
			若不为空, 将user放入session中
				判断是否勾选了自动登录
					若勾选了:
						需要将用户名和密码写回浏览器
	5.下次访问网站的时候
		过滤器拦截任意请求
			判断有无指定的cookie
				有cookie, 获取用户名和密码
				调用service完成登录操作, 返回user
				当user不为空的时候将user放入session中.

当我们换用jack登录的时候发现登录不了
	自动登录只需要登录一次:当session中没有用户的时候
	访问有些资源是不需要自动登录的(和登录还有注册相关的资源)

	修改filter的逻辑:
		首先判断session中是否有user
			若没有 并且访问的路径不是和登录注册相关的时候
				才去获取指定的cookie
///////////////////////////////////////////////////////
///////////////////////////////////////////////////////
filter总结
	filterConfig:(了解)
		过滤器的配置对象
		作用:
			获取全局管理者
			获取当前filter的名称
			获取当前filter的初始化参数

	filter-mapping的子标签(理解)
		servlet-name:匹配那个servlet 值写的是serlvet标签中servlet-name的值
			建议:不要在一个filter中重复的匹配servlet
				例如: serlvet的url-pattern为  /a/b/hello   serlvetname:HelloServlet
					如果filter中的url-pattern  /*
					最好不要在写 servlet-name:HelloServlet

		dispatcher:
			匹配哪种请求
			默认的是REQUEST, 一旦显式的写出来哪种请求, 默认就不起作用了
				理解
					REQUEST:从浏览器发送过来的请求(默认) 理解
					FORWARD:转发过来的请求 理解

				了解
					ERROR:因服务器错误而发送过来的请求
					INCLUDE:包含过来的请求
//////////////////////////
//////////////////////////
案例2-统一字符编码
需求:
	以前我们开发的时候若有参数, 第一步都是设置编码, 才不会出现乱码, 通过过滤器设置, 到servlet或者jsp上的时候已经没有乱码问题
技术分析:
	filter 配置路径/* 过滤器的第一个位置
	在filter中重写getParameter(加强)
步骤分析:
	我们只需要在filter中 对request进行加强(例如:只对request.getParameter()进行加强)

	方法加强:
		1.继承(获取构造器)
		2.装饰者模式(静态代理)
		3.动态代理

	装饰者书写步骤:
		1.要求装饰者和被装饰者实现同一个接口或者继承同一个类
		2.装饰者中要有被装饰者的引用
		3.对需要加强方法进行加强
		4.对不需要加强的方法调用原来的方法即可

	加强request.getParameter(String key)
		首先请求的方式不同, 处理的方式也不同
			获取请求的方法
			若是get请求
				new String(value.getBytes("iso8859-1"), "utf-8");
			若是post请求
				只需要设置一句话
				request.setCharacterEncoding("utf-8");

	最后将包装过的request对象(MyRequest)传递给servlet即可
///////////////////////////////////////////////////
///////////////////////////////////////////////////
 关于获取参数的方法
	String getParameter(String name);// arr[0]
	String[] getParameterValues(String name);// map.get(name)
	Map<String, String[]> getParameterMap();



//////////////////////////////////////////////
//////////////////////////////////////////////
listener 监听器
	监听javaweb中的三个域对象

	监听三个对象的创建和销毁
		ServletContextListener
		ServletRequestListener
		HttpSessionListener
	监听三个对象属性的变化
		ServletContextAttributeListener
		ServletRequestAttributeListener
		HttpSessionAttributeListener
	监听javabean在session中的状态
		注意:	javabean实现的接口 不需要在web.xml中配置
		HttpSessionActivationListener(javabean的活化和钝化)
		HttpSessionBindingListener(添加到session中还是从session中移除)

	编写步骤:
		1.编写一个类  实现接口
		2.编写配置文件
			<listener>
				<listener-class>....
			</listener>
/////////////////////////////////////
filter 接口, 过滤请求和响应
	编写步骤:
		1.编写一个类
			实现filter 重写三个方法
		2.编写配置文件
			注册filter 绑定路径
	filter的生命周期(了解)
	url-pattern配置(和servlet一样)
		一个路径匹配到多个filter的时候, 执行顺序有有web.xml中filter-mapping的顺序来决定的
	FilterChain
		必须放行才有可能到下一个Filter或者资源上
