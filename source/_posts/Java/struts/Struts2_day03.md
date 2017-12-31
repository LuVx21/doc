---
title: Struts
date: 2017-11-11
tag:
- Java
- Struts
---

<!-- TOC -->

- [OGNL表达式](#ognl表达式)
    - [基本使用示例](#基本使用示例)
    - [在Struts2框架中使用OGNL表达式](#在struts2框架中使用ognl表达式)
- [值栈(ValueStack)](#值栈valuestack)
    - [关于](#关于)
    - [内部结构](#内部结构)
    - [值栈的创建及与ActionContext对象的关系](#值栈的创建及与actioncontext对象的关系)
    - [获取值栈对象](#获取值栈对象)
    - [向值栈中保存数据](#向值栈中保存数据)
    - [从值栈中获取数据](#从值栈中获取数据)
        - [使用示例代码](#使用示例代码)
    - [EL表达式获取值栈数据](#el表达式获取值栈数据)
- [GNL表达式的特殊符号](#gnl表达式的特殊符号)

<!-- /TOC -->


# OGNL表达式

OGNL(Object Graphic Navigation Language):对象图导航语言

* 所谓对象图, 即以任意一个对象为根, 通过OGNL可以访问与这个对象关联的其它对象
* 通过它简单一致的表达式语法, 可以存取对象的任意属性, 调用对象的方法, 遍历整个对象的结构图, 实现字段类型转化等功能.它使用相同的表达式去存取对象的属性
* Struts2框架使用OGNL作为默认的表达式语言
* OGNL是一种比EL强大很多倍的语言
* xwork提供 OGNL表达式
* 使用需要引入`ognl-3.0.5.jar`

OGNL的五大功能

* 支持对象方法调用
* 支持类静态的方法调用和值访问
* 访问OGNL上下文(OGNL context)和ActionContext
* 支持赋值操作和表达式串联
* 操作集合对象

## 基本使用示例

```java
	// 访问对象的方法
	@Test
	public void run1() throws OgnlException{
		OgnlContext context = new OgnlContext();
		// 获取对象的方法
		Object obj = Ognl.getValue("'helloworld'.length()", context, context.getRoot());
		System.out.println(obj);
	}

	// 获取OGNL上下文件的对象
	@Test
	public void run3() throws OgnlException{
		OgnlContext context = new OgnlContext();
		context.put("name", "甲");
		// 获取对象的方法
		Object obj = Ognl.getValue("#name", context, context.getRoot());
		System.out.println(obj);
	}

	// 从root栈获取值
	@Test
	public void demo3() throws OgnlException{
		OgnlContext context = new OgnlContext();
		Customer c = new Customer();
		c.setCust_name("haha");
		context.setRoot(c);
		String name = (String) Ognl.getValue("cust_name", context, context.getRoot());
		System.out.println(name);
	}

```

## 在Struts2框架中使用OGNL表达式

Struts2引入了OGNL表达式, 主要是在JSP页面中获取值栈中的值

使用方法:

```jsp
// 引入Struts2的标签库
 <%@ taglib prefix="s" uri="/struts-tags" %>
// 使用Struts2提供的标签中的标签
<s:property value="OGNL表达式"/>
// 在JSP页面使用OGNL表达式
// 访问对象方法
<s:property value="'hello'.length()"/>
```


# 值栈(ValueStack)

## 关于

* `ValueStack` 是 struts2 提供一个接口, 实现类 `OgnlValueStack` ---- 值栈对象 (OGNL是从值栈中获取数据的 )
* Action是多例的, 有一起请求, 创建Action实例, 创建一个`ActionContext`对象, 代表的是Action的上下文对象, 还会创建一个ValueStack对象.
* 每个Action实例都有一个ValueStack对象 (一个请求对应一个ValueStack对象 )
* 值栈中保存当前Action 对象和其他相关对象
* Struts 框架把 ValueStack 对象保存在名为 `struts.valueStack`的请求属性中(值栈对象是request一个属性)
```java
// 能够获取值栈对象, 但有更好方式, 很少使用
ValueStack vs = (ValueStack)request.getAttribute("struts.valueStack");
```

## 内部结构

1. 值栈由两部分组成

* `root`:Struts把动作和相关对象压入 ObjectStack 中--List
* `context`:Struts把各种各样的映射关系(一些 Map 类型的对象) 压入 ContextMap 中

> CompoundRoot 就是ArrayList
> OgnlContext 就是 Map

2. Struts会默认把下面这些映射压入ContextMap(context)中

* `parameters`:该 Map 中包含当前请求的请求参数, `?name=xxx&password=123`
* `request`:该 Map 中包含当前 request 对象中的所有属性
* `session`:该 Map 中包含当前 session 对象中的所有属性
* `application`:该 Map 中包含当前 application  对象中的所有属性
* `attr`:该 Map 按如下顺序来检索某个属性, `request, session, application`

3. 访问值栈中的数据

* 访问`root`中数据时不需要使用`#`
* 访问request, session, application, attr, parameters对象数据时必须写`#`

> 操作值栈默认指操作`root`元素

## 值栈的创建及与ActionContext对象的关系

* 值栈对象是请求时创建的
* ActionContext是绑定到当前的线程上, 那么在每个拦截器或者Action中获取到的ActionContext是同一个.
* ActionContext中存在一个Map集合, 该Map集合和ValueStack的context是同一个地址.
* ActionContext中可以获取到ValueStack的引用, 使用ActionContext来获取到值栈对象

> 也是开发中常用的获取值栈对象的方式

## 获取值栈对象

三种方法:

```java
// 不常用
ValueStack vs1 = (ValueStack) ServletActionContext.getRequest().getAttribute("struts.valueStack");
ValueStack vs2 = (ValueStack) ServletActionContext.getRequest().getAttribute(ServletActionContext.STRUTS_VALUESTACK_KEY);
// 常用
ValueStack vs3 = ActionContext.getContext().getValueStack();
```

## 向值栈中保存数据

主要针对root栈

```java
valueStack.push(Object obj);
```
push方法的底层调用root对象的push方法放在栈顶(把元素添加到0位置)

```java
valueStack.set(String key, Object obj);
```
通过查看源码可知, 
首先获取map集合(map有可能是已经存在的, 有可能是新创建的), 把map集合push到栈顶, 再把数据存入到map集合中.

> 在jsp中 通过 `<s:debug />` 查看值栈的内容

## 从值栈中获取数据

需要明确的问题:

* 访问root中数据 不需要#
* 访问context其它对象数据 加 #
* 如果向root中存入对象的话, 优先使用push方法.
* 如果向root中存入集合的话, 优先要使用set方法.

从`OgnlContext`中获取数据:

* `request`:`<s:property value="#request.username"/>`
* `session`:`<s:property value="#session.username"/>`
* `application`:`<s:property value="#application.username"/>`
* `attr`:`<s:property value="#attr.username"/>`
* `parameters`:`<s:property value="#parameters.cid"/>`

### 使用示例代码

```jsp
<!--
	// vs.push("甲");
	// 获取到栈顶的值
	<s:property value="[0].top"/>
-->

<!--
	// 栈顶是map集合, 通过key获取值
	vs.set("msg", "乙");
	<s:property value="[0].top.msg"/>
-->

<!--
	vs.push(user);
	// 栈顶放user对象
	<s:property value="[0].top.username"/>
	<s:property value="[0].top.password"/>
	// [0].top 关键字是可以省略的  findValue()
	<s:property value="username"/>
-->

<!--
	vs.set("user", user);
	<s:property value="[0].top.user.username"/>
	<s:property value="[0].top.user.password"/>
	// 省略关键字
	<s:property value="user.username"/>
-->

<!--
	// 在ValueStack1Action提供了成员的属性
	private User user = new User("小泽", "456");
	public User getUser() {
		return user;
	}
	public void setUser(User user) {
		this.user = user;
	}

	User user = new User("小苍", "123");
	vs.set("user", user);
	// 从栈顶开始查找, 找user的属性, 显示名称	返回的小苍
	<s:property value="user.username"/>

	// [1].top获取ValueStack1Action [1].top.user返回user对象  [1].top.user.username获取对象的属性名称
	<s:property value="[1].top.user.username"/>
-->

<!--
	栈顶是list集合
	vs.push(ulist);
	<s:property value="[0].top[0].username"/>
	<s:property value="[0].top[1].username"/>
-->

<!--
	vs.set("ulist", ulist);
	<s:property value="ulist[0].username"/>
-->

<!-- 迭代的标签
	属性
		* value	要迭代的集合, 需要从值栈中获取
		* var	迭代过程中, 遍历的对象
			* var编写上, 把迭代产生的对象默认压入到context栈中, 从context栈取值, 加#号
			* var不编写, 默认把迭代产生的对象压入到root栈中

	for(User user:ulist){}
	// 编写var的属性
	<s:iterator value="ulist" var="u">
		<s:property value="#u.username"/>
		<s:property value="#u.password"/>
	</s:iterator>

	// 没有编写var关键字
	<s:iterator value="ulist">
		<s:property value="username"/>
		<s:property value="password"/>
	</s:iterator>
-->

<!-- 从context栈中获取值, 加#号

HttpServletRequest request = ServletActionContext.getRequest();
request.setAttribute("msg", "甲");
request.getSession().setAttribute("msg", "小风");

<s:property value="#request.msg"/>
<s:property value="#session.msg"/>
<s:property value="#parameters.id"/>
<s:property value="#attr.msg"/>
-->

<!-- 在JSP页面上, 查看值栈的内部结构 -->
<s:debug></s:debug>
```

> `[0].top.`可以省略, 默认取栈顶对象

## EL表达式获取值栈数据


> 为什么EL也能访问值栈中的数据？

StrutsPreparedAndExecuteFilter的doFilter代码中
```java
request = prepare.wrapRequest(request);
```	
> 对Request对象进行了包装 , StrutsRequestWrapper
```java
Object attribute = super.getAttribute(s);
if (attribute == null) {
	attribute = stack.findValue(s);
}
```
> 增强了request的 `getAttribute()`方法
> 访问request范围的数据时, 如果数据找不到, 去值栈中找
> request对象 具备访问值栈数据的能力 (查找root的数据)

# GNL表达式的特殊符号

## `#`符号

* 获得contextMap中的数据
* 构建一个map集合
```jsp
<s:radio name="sex" list="{'男', '女'}"></s:radio>
<s:radio name="sex" list="#{'0':'男', '1':'女'}"></s:radio>
```

## `%`符号


强制字符串解析成OGNL表达式
```jsp
例如:在request域中存入值, 然后在文本框(<s:textfield>)中取值, 现在到value上.
<s:textfield value="%{#request.msg}"/>
```

> `{ }`中值用''引起来, 此时不再是ognl表达式, 而是普通的字符串
> 如:`<s:property value="%{'#request.msg'}"/>`

## `$`符号

在配置文件中可以使用OGNL表达式
如:文件下载的配置文件.
```xml
<action name="download1" class="me.ren.demo.DownloadAction">
	<result name="success" type="stream">
		<param name="contentType">${contentType}</param>
		<param name="contentDisposition">attachment;filename=${downFilename}</param>
	</result>
</action>
```
