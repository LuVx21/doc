---
title: Servlet
date: 2015-0628
tags:
- Java
---
<!-- TOC -->

- [生命周期](#生命周期)
- [forward & forward](#forward--forward)
- [JSP](#jsp)
- [域对象](#域对象)
- [引入页面](#引入页面)
- [JSP & Servlet](#jsp--servlet)

<!-- /TOC -->

# 生命周期

```Java
public void init(ServletConfig config)
public ServletConfig getServletConfig()
public String getServletInfo()
public void service(ServletRequest request,ServletResponse response)
public void destroy()
```

# forward & forward

||forward|forward|
|:---|:---|:---|
||||
|数据共享|转发页面和转发到的页面可以共享request里面的数据.只能在同一个Web应用程序内的资源之间转发请求.|不能共享数据.并且不仅可以重定向到当前应用程序的其他资源,还可以重定向到同一个站点上的其他应用程序中的资源, 甚至是使用绝对URL重定向到其他站点的资源.|
||服务器内部的一种操作|服务器通知客户端,让客户端重新发起请求|
|使用场景|用于用户登陆的时候,根据角色转发到相应的模块.|用户注销登陆时返回主页面和跳转到其它的网站等.|
|效率|高|低|
forward是服务器内部请求资源,服务器直接访问目标地址的URL,把那个URL的响应内容读取过来,然后把这些内容再发给浏览器,浏览器根本不知道服务器发送的内容是从哪儿来的,所以它的地址栏中还是原来的地址.

redirect就是服务端根据逻辑,发送一个状态码,告诉浏览器重新去请求那个地址,一般来说浏览器会用刚才请求的所有参数重新请求,所以session,request参数都可以获取.

> forward也有缺点,就是forward的页面的路径如果是相对路径就会有些问题了

(1) 带request参数的跳转
request.setAttribute("param", "value");
request.getRequestDispatcher("test.jsp").forward(request, response);
在目的页面可以获取参数
String Value = request.getAttribute("param")==null?:(String)request.getAttribute("param");
(2) 不带request参数的跳转
response.sendredirect("test.jsp");

servlet的转发方式有两种:

response.sendRedirect(response.encodeURL(相对路径或绝对路径));
request.getRequestDispatcher(相对路径).forward();

jsp是servlet的扩展,除可用上面两种之外,还可以用标签:`<jsp:forward page="相对路径" />`

# JSP

JSP 共有以下6种基本动作:

* jsp:include: 在页面被请求的时候引入一个文件.
* jsp:useBean: 寻找或者实例化一个JavaBean.
* jsp:setProperty: 设置JavaBean的属性.
* jsp:getProperty: 输出某个JavaBean的属性.
* jsp:forward: 把请求转到一个新的页面.
* jsp:plugin: 根据浏览器类型为Java插件生成OBJECT或EMBED标记

# 域对象

```jsp
<%@page language="java" contenType="text/html;charset=gb2312" session="true" buffer="64kb" autoFlush="true"
isThreadSafe="true" info="text" errorPage="error.jsp" isErrorPage="true" isELIgnored="true" pageEncoding="gb2312"
import="java.sql.*"%>
```

* request用户端请求,此请求会包含来自GET/POST请求的参数
* response 网页传回用户端的回应
* pageContext 网页的属性是在这里管理
* session 与请求有关的会话期
* application servlet 正在执行的内容
* out 用来传送回应的输出
* config servlet的构架部件
* page JSP网页本身
* exception 针对错误网页,未捕捉的例外


# 引入页面

```jsp
// 动态
<jsp:include page="b.jsp" /> 
// 静态
<%@ include file="b.jsp" /> 
```

1:
动态引入是先执行,后包含,能动态区别加进来的是动态页面还是静态页面,对于静态页面则直接将资源包含(仅取其文本).
静态引入则静态地包含页面,不管是静态页面还是动态页面都首先将页面的内容加进来.  

2:
动态引入可以传递参数
```jsp
<jsp:include page="b.jsp" >
   <jsp:param name="参数名" value="参数值"/>
</jsp:include>
```

# JSP & Servlet

