---
title: Java ajax
date: 2017-10-27
tags:
- Java
- Web
- Ajax
---

# 关于

需求:
	当我们在注册页面上输入用户名之后,点击下一个地方,去数据库中查询有无该用户名,最后提示信息
技术分析:
	ajax


**ajax:**

	异步JavaScript和XML,
	AJAX 是一种用于创建快速动态网页的技术。
	通过在后台与服务器进行少量数据交换，AJAX 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。
	传统的网页（不使用 AJAX）如果需要更新内容，必须重载整个网页页面。

# 使用

## 原生Ajax
ajax入门程序:
	步骤:
		1.创建一个核心对象 XMLHttpRequest
		2.编写一个回调函数
		3.编写请求方式和请求的路径(open操作)
		4.发送请求(send操作)
ajax-api详解
	常用属性:
		onreadystatechange:检测readyState状态改变的时候
		readyState:ajax核心对象的状态
			0:核心对象创建
			1:调用了open方法
			2:调用了send方法
			3:部分响应已经生成(没有意思)
			4:响应已经完成(使用的是这个状态)
		status:状态码
			if(xmlhttp.readyState==4 && xmlhttp.status==200){
			}
		responseText:响应回来的文本
	常用方法:
		open("请求方式","请求路径"[,"是否异步"]):设置请求的方式和请求的路径
		send(["参数"]):发送请求 参数是请求方式为post的时候的参数
		setRequestHeader("content-type","form表单enctype属性"):设置post请求的参数的类型 必须放在send方法之前.
/////////////////////////////
步骤分析:
	1.数据库和表
		CREATE TABLE `user` (
		  `id` INT(11) NOT NULL AUTO_INCREMENT,
		  `username` VARCHAR(20) DEFAULT NULL,
		  `password` VARCHAR(20) DEFAULT NULL,
		  `email` VARCHAR(20) DEFAULT NULL,
		  `name` VARCHAR(20) DEFAULT NULL,
		  `sex` VARCHAR(10) DEFAULT NULL,
		  `birthday` DATE DEFAULT NULL,
		  `hobby` VARCHAR(50) DEFAULT NULL,
		  PRIMARY KEY (`id`)
		) ENGINE=INNODB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;
		INSERT
		INTO `user`(`id`,`username`,`password`,`email`,`name`,`sex`,`birthday`,`hobby`)
		VALUES
		(1,'bbb','123','123@163.com','张三','男','1992-01-02','篮球, 足球, 排球'),
		(2,'ccc','123','ccc@itcast.cn','李四','女','1992-03-20','排球, 乒乓球'),
		(3,'aaa','123','aaa@itcast.cn','王守义','男','1990-08-11','足球, 排球'),
		(5,'tom','123','haha@qq.com','提莫','男',NULL,'篮球');
	2.新建项目
		导入 jar包 工具类 配置文件
	3.新建一个注册页面 表单 在用户名文本框上输入用户名 失去焦点
		发送ajax请求,将输入的值发送到servlet
	4.checkUsername4Ajax
		接受用户名
		调用service完成查询操作 返回一个用户
		判断user是否为空
			若为空 :写1 代表可以使用
			若不为空:写0
	5.在表单接受响应的数据
		判断一下,
			若为0,则提示用户名已被占用 表单不可用提交 提交按钮禁用
				document.getElementById("sub").disabled=true;

## jquery中的ajax

四种:
	了解:jquery对象.load(url,params,function(数据){});
	★: $.get(url,params,function(数据){},type);
		发送get请求的ajax
			url:请求的路径
			params:请求的参数 参数为key\value的形式 key=value  {"":"","":""}
			fn:回调函数 参数就是服务器发送回来的数据
			type:返回内容格式，xml, html, script, json, text, _default。    以后用"json"

	★: $.post(url,params,function(数据){},type);
		发送post请求的ajax

		若结果为json格式,  打印返回值的时候是一个对象
			例如若json为 {"result":"success","msg":"成功"}
			获取msg 只需要	参数.msg
	理解:
		$.ajax([选项]);
			选项的可选值:
				url:请求路径
				type:请求方法
				data:发送到服务器的数据
				success:fn 成功以后的回调
				error:fn 异常之后的回调
				dataType:返回内容格式 经常使用json
				async:设置是否是异步请求
			例如:
				$.ajax({
					url:"/day15/demo1",
					type:"post",
					data:"username=tom",
					success:function(d){
						alert(d.msg);
					},
					error:function(){},
					dataType:"json"

				});

//////////////////////
步骤分析:
	将js原生ajax修改成jquery的ajax
////////////////////////////////
案例3-模仿百度搜索
需求:
	在一个文本框中输入一段内容,keyup的时候发送一个ajax请求,去数据库中查找相应的内容,在页面上展示
步骤分析:
	1.表
		create table keyword(
			id int primary key auto_increment,
			kw varchar(20)
		);
	2.页面
	3.在文本框输入内容 keyup的时候 发送ajax请求 将输入值传递到后台
	4.将返回的数据展示
///////////////////////////////////////
案例4-省市联动
需求:
	先有一个省份的下拉选,根据选择省份,从而动态的市下拉选中加载所有的市.
步骤分析:
	1.表
	2.页面上有两个下拉选 省份的下拉选一般是固定的 页面加载的时候读取所有的省份
	3.当省份改变的时候,获取省份的信息,发送一个ajax请求,去市的表中查询相应省份的所有市,然后将他们加载到市下拉选上
	4.selectProServlet selectCityServlet
	//////////////////////
技术分析:
	json
		JSON(JavaScript Object Notation) 是一种轻量级的数据交换格式。它基于ECMAScript的一个子集。
	json格式:
		格式1:value可以为任意值
			{"key":value,"key1":value1}
		格式2:e可以为任意值
			[e1,e2]
	jsonlib工具类,可以使对象转换成json数据
		1.导入jar包
		2.使用api
			JSONArray.fromObject(对象)  数组和list
			JSONObject.fromObject(对象) bean和map

/////////////////////////////////////////////////////////
/////////////////////////////////////////////////////////
上午回顾:
原生ajax:
	1.获取核心对象 XMLHttpRequest
	2.编写回调函数
		xmlhttp.onreadystatechange=function(){
			if(xmlhttp.readyState==4 && xmlhttp.status==200){
				alert(xmlhttp.responseText);
			}
		}
	3.open操作 设置访问的方式和路径
		xmlhttp.open("get","url");
	4.send操作
		xmlhttp.send("post的参数")
		 若有参数需要设置一个头
			xmlhttp.setRequestHeader("content-type","form表单enctype属性")
////////////////////////
jquery中ajax
	掌握的:
		$.get(url,params,fn,type);
			type:"json"
		$.post(url,params,fn,type);

	理解:
		$.ajax(选项);
			选项:
				url:
				type:
				data:
				success:fn
				error:
				dataType:"json"
json:轻量级的数据格式
	格式1:
		{"key":value,"key1":value}
	格式2:
		[e1,e2]
jsonlib:
	1.导入jar包
	2.使用api
		JSONArray.formObject(数组或者list);
		JSONObject.formObject(bean或者map);

//////////////////////////////////////////
获取jquery对象:
	$("选择器") jQuery("选择器");
jquery对象>>dom对象
	方式1:
		jquery对象.get(index);

	方式2:
		jquery对象[index]
dom对象>>jquery对象
	$(dom对象)

页面载入
	$(function(){})

派发事件
	jquery对象.事件(function(){...});

选择器:
	#id值  .class值  标签名  [属性]  [属性='值']
	a b:后代    a>b:孩子  a+b:大弟弟  a~b:所有弟弟
	:first :last :odd :even :eq|gt|lt(index)
	:hidden
	:checked  :selected
属性和css:
	prop|attr
	css

文本 标签体
	val()
	html() text()

文档处理
	内部插入
		append prepend
	外部插入
		after before
	删除
		remove
效果:
	隐藏|显示
		show() hide()
	淡入淡出
		fadeIn() fadeOut()
	滑入滑出
		slideDown() slideUp()

遍历
	jquery对象.each(function(){
	});
