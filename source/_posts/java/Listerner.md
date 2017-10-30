---
title: Java Listener
date: 2017-10-29
tags:
- Java
- Listener
- Web
---


回顾:
ajax:
	异步请求
原生的ajax(了解)
	1.创建一个核心对象 XMLHttpRequest
	2.编写回调函数
		xmlhttp.onreadystatechange=function(){
			if(xmlhttp.readyState==4 && xmlhttp.status==200){
				alert(xmlhttp.responseText);
			}
		}
	3.open操作
		xmlhttp.open("请求方式","请求路径");
	4.send操作
		xmlhttp.send([参数]);
			post请求的参数放在send里面
			注意:若请求方式为post且有参数 必须设置一个头
jquery中ajax
	掌握:
		$.get(url,params,function(data){},type);
			type:以后常用的是json
		$.post(url,params,function(data){},type);
	了解:
		jquery对象.load(...)
	理解:
		$.ajax(选项)
			选项:
				url
				type
				data
				success:fn
				error:fn
				dataType:"json"
/////////////////////////////////////////////////////////////
/////////////////////////////////////////////////////////////
listener(了解) 监听器
filter(★)	过滤器
//////////////////////////
listener:
	监听器
	作用:
		监听web中中的域对象 ServletContext ServletRequest HttpSession
	监听内容:
		1. 监听三个对象的创建和销毁(生命周期)
		2. 监听三个对象属性的变化
		3. 监听session中javabean的状态

	> 注意:listener全部是接口

监听三个对象的创建和销毁

	* ServletContextListener
	* ServletRequestListener
	* HttpSessionListener

监听三个对象属性的变化

	* ServletContextAttributeListener
	* ServletRequestAttributeListener
	* HttpSessionAttributeListener

监听session中javabean的状态

	* HttpSessionActivationListener(钝化和活化)
	* HttpSessionBindingListener(绑定和解绑)

使用步骤:
	1.编写一个类 实现接口
	2.重写方法
	3.编写配置文件(大部分都是)

演示各个监听器
### 监听三个对象的创建和销毁
	ServletContextListener
		创建:服务器启动的时候,会为每一个项目都创建一个servletContext
		销毁:服务器关闭的时候,或者项目被移除的时候
		以后用来加载配置文件
	ServletRequestListener
		创建:请求来的时候
		销毁:响应生成的时候
	HttpSessionListener
		创建:
			java中第一次调用request.getSession的时候
			jsp访问的时候创建
		销毁:
			三种情况:
				session超时
				手动销毁session
				服务器非正常关闭

### 监听三个对象属性的变化(添加 替换 删除)
	ServletContextAttributeListener
	ServletRequestAttributeListener
	HttpSessionAttributeListener

### 监听session中javabean的状态
	注意:这两个接口需要javabean实现.是让javabean感知到自己的状态

	HttpSessionBindingListener(绑定和解绑)
		检测java是否添加到session或者从session中移除
	HttpSessionActivationListener(钝化和活化)
		钝化:javabean从session中序列化到磁盘上
		活化:javabean从磁盘上加载到了session中
		> javabean需要实现序列化接口

		可以通过配置文件修改javabean什么时候钝化(合理分配服务器内存)
			修改一个项目
				只需要在项目下/meta-info创建一个context.xml
				内容如下:
```xml
	<Context>
		<!--
			maxIdleSwap	:1分钟 如果session不使用就会序列化到硬盘.
			directory	:itheima 序列化到硬盘的文件存放的位置.
		-->
		<Manager className="org.apache.catalina.session.PersistentManager" maxIdleSwap="1">
			<Store className="org.apache.catalina.session.FileStore" directory="itheima"/>
		</Manager>
	</Context>
```

//////////////////////////////////////////////////
案例1-自动登录
需求:
	登录的时候,勾选自动登录,登录成功之后,关闭浏览器,下一次访问网站的时候完成登录操作(自动登录).
技术分析:
	filter
	cookie