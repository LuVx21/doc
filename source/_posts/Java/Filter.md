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
- [Filter](#filter)
- [案例1:自动登录](#案例1自动登录)
- [案例2:统一字符编码](#案例2统一字符编码)

<!-- /TOC -->
# 关于

过滤请求和响应
作用:	自动登录,统一编码,过滤关键字等作用

> Filter是一个接口

# Filter

方法:

* init(FilterConfig config):初始化操作
* doFilter(ServletRequest request, ServletResponse response, FilterChain chain):处理业务逻辑
* destroy() :销毁操作

filter的生命周期

* 单实例多线程
* 在服务器启动的时候 服务器创建filter 调用init方法 实现初始化操作
* 请求来的时候, 创建一个线程 根据路径调用dofilter 执行业务逻辑
* 当filter被移除的时候或者服务器正常关闭的时候 调用destory方法执行销毁操作.

FilterChain:过滤链

* 通过chain的dofilter方法, 可以将请求放行到下一个过滤器, 直到最后一个过滤器放行才可以访问servlet或jsp
* doFilter()为放行方法

url-pattern配置

* 完全匹配: 必须以`/` 开始,例: /a/b
* 目录匹配: 必须以`/` 开始 以`*`结束,例:/a/b/*
* 后缀名匹配: 以`*.`开始 以字符结束,例: *.jsp  *.do  *.action

★一个资源有可能被多个过滤器匹配成功, 多个过滤器的执行顺序是按照web.xml中filter-mapping的顺序执行的

web.xml中的配置
```xml
<filter>
	<filter-name>AutoLoginFilter</filter-name>
	<filter-class>org.luvx.filter.AutoLoginFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>AutoLoginFilter</filter-name>
	<url-pattern>/*</url-pattern>
	<dispatcher>FORWARD</dispatcher>
	<dispatcher>REQUEST</dispatcher>
</filter-mapping>
```

* filter-name:过滤指定的servlet
* dispatcher:过滤哪种方式过来的请求
* REQUEST:只过滤从浏览器发送过来的请求 (默认) 一旦显式的写出来disparcher 默认的就不起作用了
* FORWARD::只过滤请求转发过来的请求

# 案例1:自动登录

```sql
		create database day16;
		use day16;
		create table user(
			id int primary key auto_increment,
			username varchar(20),
			password varchar(20)
		);
```
画面上有'记住用户名'和'自动登录'两个多选框

LoginServlet.Java

1. 接受用户名和密码
2. 调用service完成登录操作, 返回值User
3. 判断user是否为空
4. 若不为空, 将user放入session中
5. 判断是否勾选了自动登录
6. 若勾选了:
7. 需要将用户名和密码写回浏览器

```Java
public class LoginServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		//0.设置编码
		request.setCharacterEncoding("utf-8");

		//1.获取用户名和密码
		String username=request.getParameter("username");
		String password=request.getParameter("password");

		//2.调用service'
		User user = null;
		try {
			user = new UserService().login(username,password);
		} catch (SQLException e) {
			e.printStackTrace();
		}

		//3.判断user是否为空
		if(user == null){
			request.setAttribute("msg", "用户名和密码不匹配");
			request.getRequestDispatcher("/login.jsp").forward(request, response);
			return;
		}else{
			request.getSession().setAttribute("user", user);
			//判断是否勾选了自动登录  若勾选了需要将用户名和密码放入cookie中, 写回浏览器
			if("ok".equals(request.getParameter("autoLogin"))){
				//创建cookie 注意中文
				Cookie c=new Cookie("autologin", username+"-"+password);
				c.setMaxAge(3600);
				c.setPath(request.getContextPath()+"/");

				response.addCookie(c);
			}

			//判断是否勾选了记住用户名 若勾选了需要将用户名放入cookie中 写回浏览器
			if("ok".equals(request.getParameter("saveName"))){
				//创建cookie
				Cookie c=new Cookie("savename", URLEncoder.encode(username, "utf-8"));
				c.setMaxAge(3600);
				c.setPath(request.getContextPath()+"/");

				response.addCookie(c);
			}
			//页面重定向
			response.sendRedirect(request.getContextPath()+"/success.jsp");
		}
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		doGet(request, response);
	}
}

```

AutoLoginFilter.Java

1. 过滤器拦截任意请求
2. 判断有无指定的cookie
3. 有cookie, 获取用户名和密码
4. 调用service完成登录操作, 返回user
5. 当user不为空的时候将user放入session中

```Java
public class AutoLoginFilter implements Filter{
	@Override
	public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain)
			throws IOException, ServletException {
		//1.强转
		HttpServletRequest request =(HttpServletRequest) req;
		HttpServletResponse response =(HttpServletResponse) resp;

		//2.完成自动登录
		//2.1 判断session中有无登录登录用户 没有的话继续自动登录
		User user = (User) request.getSession().getAttribute("user");
		if(user == null){
			//没有用户  需要自动登录
			//2.2 判断访问的资源是否和登录注册相关,若相关则不需要自动登录
			String path = request.getRequestURI();
			if(!path.contains("/login")){

				//2.3获取指定的cookie
				Cookie c = CookUtils.getCookieByName("autologin", request.getCookies());
				//判断cookie是否为空
				//若不为空 获取值(username和passowrd) 调用serivce完成登录  判断user是否为空 不为空 放入session
				if(c != null){
					String username=c.getValue().split("-")[0];
					String password=c.getValue().split("-")[1];

					//调用serivce完成登录
					try {
						user = new UserService().login(username, password);
					} catch (SQLException e) {
						e.printStackTrace();
					}

					if(user!=null){
						//将user放入session中
						request.getSession().setAttribute("user", user);
					}
				}
			}
		}
		//3.放行
		chain.doFilter(request, response);
	}

	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
	}

	@Override
	public void destroy() {
	}

}
```


# 案例2:统一字符编码

分析:
	filter 配置路径/* 过滤器的第一个位置
	在filter中重写getParameter(加强:只对request.getParameter()进行加强)

方法加强的方式:

1. 继承(获取构造器)
2. 装饰者模式(静态代理)
3. 动态代理

装饰者书写步骤:

1. 要求装饰者和被装饰者实现同一个接口或者继承同一个类
2. 装饰者中要有被装饰者的引用
3. 对需要加强方法进行加强
4. 对不需要加强的方法调用原来的方法即可

```Java
public class EncodingFilter1 implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
    }

    @Override
    public void doFilter(ServletRequest req, ServletResponse resp, FilterChain chain)
            throws IOException, ServletException {
        //1.强转
        final HttpServletRequest request = (HttpServletRequest) req;
        HttpServletResponse response = (HttpServletResponse) resp;

        //创建代理对象
        HttpServletRequest requestProxy = (HttpServletRequest) Proxy.newProxyInstance(HttpServletRequest.class.getClassLoader(), request.getClass().getInterfaces(), new InvocationHandler() {

            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                if ("getParameter".equals(method.getName())) {
                    String m = request.getMethod();

                    if ("get".equalsIgnoreCase(m)) {
                        String s = (String) method.invoke(request, args);//相当于  request.getParameter(args);
                        return new String(s.getBytes("iso8859-1"), "utf-8");
                    } else if ("post".equalsIgnoreCase(m)) {
                        request.setCharacterEncoding("utf-8");
                        return method.invoke(request, args);
                    }
                }
                return method.invoke(request, args);
            }
        });

        //2.放行
        chain.doFilter(requestProxy, response);
    }

    @Override
    public void destroy() {
    }

}
```
