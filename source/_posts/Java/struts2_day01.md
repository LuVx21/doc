## Struts2第一天 ##
	
----------
	
**Struts2的学习路线**
	
	1. Struts2的入门：主要是学习Struts2的开发流程（Struts2的开发流程、常见的配置、Action类的编写）
	2. Struts2的Servlet的API、参数封装和拦截器
	3. Struts2的值栈和OGNL表达式
	
----------
	
### 今天的课程内容 ###
	
	1. Struts2框架的概述
	2. Struts2的快速入门
	3. Struts2的运行流程
	4. Struts2的常见配置（配置文件加载的顺序、struts.xml的常见配置（重点）、常量的配置等）
	5. Struts2的Action的访问（Action的三种编写方式、Action类的配置、方法的调用等）
	
----------
	
### 案例一：使用Struts2框架完成登录功能 ###
	
----------
	
**需求分析**
	
	1. 使用Struts2完成登录的功能
	
----------
	
**技术分析之Struts2框架的概述**
	
	1. 什么是Struts2的框架
		* Struts2是Struts1的下一代产品，是在 struts1和WebWork的技术基础上进行了合并的全新的Struts 2框架。
		* 其全新的Struts 2的体系结构与Struts 1的体系结构差别巨大。
		* Struts 2以WebWork为核心，采用拦截器的机制来处理用户的请求，这样的设计也使得业务逻辑控制器能够与ServletAPI完全脱离开，所以Struts 2可以理解为WebWork的更新产品。
		* 虽然从Struts 1到Struts 2有着太大的变化，但是相对于WebWork，Struts 2的变化很小。
	
	2. Struts2是一个基于MVC设计模式的Web层框架
		* MVC和JavaEE的三层结构
			* MVC设计模式:是由一些网站的开发人员提出来的
			* JavaEE三层结构:SUN公司为EE开发划分的结构
	
	3. 常见的Web层的框架
		* Struts1
		* Struts2
		* Webwork
		* SpringMVC
	
	4. Web层框架的特点
		* 都是一个特点，前端控制器模式
		* 记住：前端控制器（核心的控制器）
		* Struts2框架前端的控制器就是过滤器
	
![](./图片/01-前端控制器模式.bmp)
	
----------
	
**技术分析之Struts2快速入门的环境准备**
	
	1. 创建WEB项目，编写JSP的页面，编写超链接，点击超链接发送请求，请求服务器，让服务器的方法去执行！！
		<h3>Struts2的入门程序</h3>
		<a href="${ pageContext.request.contextPath }/hello.action">Struts2入门程序</a>
	
	2. 下载Struts2的开发包
		* https://struts.apache.org/		-- 官网地址
	
	3. 解压struts-2.3.24-all.zip包
		* 解压后会看到有包和一些文件，大家需要掌握包相关的信息
			* apps	-- Struts2框架提供了一些应用
			* libs	-- Struts2框架开发的jar包
			* docs	-- Struts2框架开发文档
			* src	-- Struts2框架源码
	
	4. 引入需要开发的jar包
		* Struts2框架的开发jar包非常多，但是不是所有都是必须要引入的，有一些必须要导入的jar包，这些jar包可以从Struts2框架提供的应用中找到。
		* 大家可以打开apps目录，然后找到struts2-blank.war应用。war包和zip包的压缩格式是一样的，所以可以自己修改后缀名，解压。
		* 找到解压后的应用，打开WEB-INF/lib目录下所以的jar包。复制到工程中，就可以了。
	
	5. 需要配置Struts2的前端控制器，注意：这一步是必须要做的操作，这是Struts2核心的控制器。
		* Struts2的前端控制器就是一个过滤器，那么过滤器相关知识咱们都学习过，需要在web.xml中进行配置。
		* 前端控制器的类的路径和名称：org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter
		* 具体配置如下
		  <filter>
		  	<filter-name>struts2</filter-name>
		  	<filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
		  </filter>
		  <filter-mapping>
		  	<filter-name>struts2</filter-name>
		  	<url-pattern>/*</url-pattern>
		  </filter-mapping>
	
----------
	
**技术分析之编写Action类**	
	
	1. Action类是动作类，是Struts2处理请求，封装数据，响应页面的核心控制器。需要自己编写。
		package cn.itcast.action;
			public String sayHello(){
			System.out.println("Hello Struts2!!");
			return null;
		}
	
----------
	
**技术分析之编写Struts的配置文件**
	
	1. 配置文件名称是struts.xml（名称必须是struts.xml）
	2. 在src下引入struts.xml配置文件（配置文件的路径必须是在src的目录下）
	3. 配置如下
		<?xml version="1.0" encoding="UTF-8" ?>
		<!DOCTYPE struts PUBLIC
			"-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
			"http://struts.apache.org/dtds/struts-2.3.dtd">
		
		<struts>
		    <package name="default" namespace="/" extends="struts-default">
		        <action name="hello" class="com.itheima.action.HelloAction" method="sayHello">
		        </action>
		    </package>
		</struts>
	
----------
	
**入门总结之Struts2的执行流程**
	
	1. 执行的流程
		* 编写的页面，点击超链接，请求提交到服务器端。
		* 请求会先经过Struts2的核心过滤器（StrutsPrepareAndExecuteFilter）
			* 过滤器的功能是完成了一部分代码功能
			* 就是一系列的拦截器执行了，进行一些处理工作。
			* 咱们可以在struts-default.xml配置文件中看到有很多的拦截器。可以通过断点的方式来演示。
			* 拦截器执行完后，会根据struts.xml的配置文件找到请求路径，找到具体的类，通过反射的方式让方法执行。
	
	2. 总结
		* JSP页面-->StrutsPrepereAndExecuteFilter过滤器-->执行一系列拦截器（完成了部分代码）-->执行到目标Action-->返回字符串-->结果页面（result）-->页面跳转
	
![](./图片/02-执行流程.bmp)	
	
----------
	
**入门总结之struts.xml的配置文件提示的问题**
	
	1. 先找到struts.xml的配置文件，复制http://struts.apache.org/dtds/struts-2.3.dtd，注意，不要有双引号。
		* http://struts.apache.org/dtds/struts-2.3.dtd
	
	2. 选择window -- 首选项 -- 搜索xml -- 选择xml Catalog
	3. 选择添加按钮（add按钮）
		* key type 要选择URI
		* key的位置把刚才复制的路径拷贝进去。http://struts.apache.org/dtds/struts-2.3.dtd
		* Location要在本地能找到struts2-2.3.dtd的真实文件。点击File System，去资料中找到它
		
	4. 如果想查看源代码
		* 选择资料/struts2/struts-2.3.24-all.zip
	
----------
	
**入门总结之Struts2框架配置文件加载的顺序**
	
	0. 需要掌握
		* 加载了哪些个配置文件（重点的）
		* 配置文件的名称是什么
		* 配置文件的位置
		* 配置文件的作用

	1. Struts2框架的核心是StrutsPrepareAndExecuteFilter过滤器，该过滤器有两个功能
		* Prepare		-- 预处理，加载核心的配置文件
		* Execute		-- 执行，让部分拦截器执行
	
	2. StrutsPrepareAndExecuteFilter过滤器会加载哪些配置文件呢？
		* 通过源代码可以看到具体加载的配置文件和加载配置文件的顺序
			* init_DefaultProperties(); 				-- 加载org/apache/struts2/default.properties
		    * init_TraditionalXmlConfigurations();		-- 加载struts-default.xml,struts-plugin.xml,struts.xml
        	* init_LegacyStrutsProperties();			-- 加载自定义的struts.properties.
        	* init_CustomConfigurationProviders();		-- 加载用户自定义配置提供者
        	* init_FilterInitParameters() ;				-- 加载web.xml
	
	3. 重点了解的配置文件
		* default.properties		-- 在org/apache/struts2/目录下，代表的是配置的是Struts2的常量的值
		* struts-default.xml		-- 在Struts2的核心包下，代表的是Struts2核心功能的配置（Bean、拦截器、结果类型等）
		* struts.xml				-- 重点中的重点配置，代表WEB应用的默认配置，在工作中，基本就配置它就可以了！！（可以配置常量）
		* web.xml					-- 配置前端控制器（可以配置常量）
		
		* 注意：
			* 前3个配置文件是struts2框架的默认配置文件，基本不用修改。
			* 后3个配置文件可以允许自己修改struts2的常量。但是有一个特点：后加载的配置文件修改的常量的值，会覆盖掉前面修改的常量的值。
	
	4. 总结（重点掌握的配置文件）
		* 先加载default.properties文件，在org/apache/struts2/default.properties文件，都是常量。
		* 又加载struts-default.xml配置文件，在核心的jar包最下方，struts2框架的核心功能都是在该配置文件中配置的。
		* 再加载struts.xml的配置文件，在src的目录下，代表用户自己配置的配置文件
		* 最后加载web.xml的配置文件
		
		* 后加载的配置文件会覆盖掉之前加载的配置文件（在这些配置文件中可以配置常量）
	
	5. 注意一个问题
		* 哪些配置文件中可以配置常量？
			* default.properties		-- 默认值，咱们是不能修改的！！
			* struts.xml				-- 可以配置，开发中基本上都在该配置文件中配置常量
			* struts.properties			-- 可以配置，基本不会在该配置文件中配置
			* web.xml					-- 可以配置，基本不会在该配置文件中配置
		
		* 后加载的配置文件会覆盖掉之前加载的配置！！
	
----------
	
**入门总结之struts.xml配置文件的配置**
	
	1. <package>标签，如果要配置<Action>的标签，那么必须要先配置<package>标签，代表的包的概念
		* 包含的属性
			* name					-- 包的名称，要求是唯一的，管理action配置
			* extends				-- 继承，可以继承其他的包，只要继承了，那么该包就包含了其他包的功能，一般都是继承struts-default
			* namespace				-- 名称空间，一般与<action>标签中的name属性共同决定访问路径（通俗话：怎么来访问action），常见的配置如下
				* namespace="/"		-- 根名称空间
				* namespace="/aaa"	-- 带有名称的名称空间
			* abstract				-- 抽象的。这个属性基本很少使用，值如果是true，那么编写的包是被继承的
	
	2. <action>标签
		* 代表配置action类，包含的属性
			* name			-- 和<package>标签的namespace属性一起来决定访问路径的
			* class			-- 配置Action类的全路径（默认值是ActionSupport类）
			* method		-- Action类中执行的方法，如果不指定，默认值是execute
	
	3. <result>标签
		* action类中方法执行，返回的结果跳转的页面
			* name		-- 结果页面逻辑视图名称
			* type		-- 结果类型（默认值是转发，也可以设置其他的值）
	
----------
	
**入门总结之Struts2配置常量**
	
	1. 可以在Struts2框架中的哪些配置文件中配置常量？
		* struts.xml（必须要掌握，开发中基本上就在该配置文件中编写常量）
			* <constant name="key" value="value"></constant>
		* web.xml
			* 在StrutsPrepareAndExecuteFilter配置文件中配置初始化参数
		* 注意：后加载的配置的文件的常量会覆盖之前加载的常量！！
	
	2. 需要大家了解的常量
		* struts.i18n.encoding=UTF-8			-- 指定默认编码集,作用于HttpServletRequest的setCharacterEncoding方法 
		* struts.action.extension=action,,		-- 该属性指定需要Struts 2处理的请求后缀，该属性的默认值是action，即所有匹配*.action的请求都由Struts2处理。如果用户需要指定多个请求后缀，则多个后缀之间以英文逗号（,）隔开
		* struts.serve.static.browserCache=true		-- 设置浏览器是否缓存静态内容,默认值为true(生产环境下使用),开发阶段最好关闭 
		* struts.configuration.xml.reload=false		-- 当struts的配置文件修改后,系统是否自动重新加载该文件,默认值为false(生产环境下使用) 
		* struts.devMode = false					-- 开发模式下使用,这样可以打印出更详细的错误信息 
	
----------
	
**入门总结之指定多个struts的配置文件（了解）**
	
	1. 在大部分应用里，随着应用规模的增加，系统中Action的数量也会大量增加，导致struts.xml配置文件变得非常臃肿。
		为了避免struts.xml文件过于庞大、臃肿，提高struts.xml文件的可读性，我们可以将一个struts.xml配置文件分解成多个配置文件，然后在struts.xml文件中包含其他配置文件。
	
	2. 可以在<package>标签中，使用<include>标签来引入其他的struts_xx.xml的配置文件。例如：
		<struts>
			<include file="struts-part1.xml"/>
			<include file="struts-part2.xml"/>
		</struts>
	
	3. 注意注意注意（重要的事情说三遍）：<include file="cn/itcast/demo2/struts-part1.xml"/>
	
----------
	
**技术分析之Action类的三种写法**
	
	1. 配置文件学习完成，下面的重点是Action类的三种写法
		* Action类就是一个POJO类
			* 什么是POJO类，POJO（Plain Ordinary Java Object）简单的Java对象.简单记：没有继承某个类，没有实现接口，就是POJO的类。
		
		* Action类可以实现Action接口
			* Action接口中定义了5个常量，5个常量的值对应的是5个逻辑视图跳转页面（跳转的页面还是需要自己来配置），还定义了一个方法，execute方法。
			* 大家需要掌握5个逻辑视图的常量
				* SUCCESS		-- 成功.
				* INPUT			-- 用于数据表单校验.如果校验失败,跳转INPUT视图.
				* LOGIN			-- 登录.
				* ERROR			-- 错误.
				* NONE			-- 页面不转向.
		
		* Action类可以去继承ActionSupport类（开发中这种方式使用最多）
			* 设置错误信息
	
----------
	
**技术分析之Action的访问**
	
	1. 通过<action>标签中的method属性，访问到Action中的具体的方法。
		* 传统的配置方式，配置更清晰更好理解！但是扩展需要修改配置文件等！
		* 具体的实例如下：
			* 页面代码
				* <a href="${pageContext.request.contextPath}/addBook.action">添加图书</a>
				* <a href="${pageContext.request.contextPath}/deleteBook.action">删除图书</a>
			
			* 配置文件的代码
				<package name="demo2" extends="struts-default" namespace="/">
			    	<action name="addBook" class="cn.itcast.demo2.BookAction" method="add"></action>
			    	<action name="deleteBook" class="cn.itcast.demo2.BookAction" method="delete"></action>
			    </package>
			
			* Action的代码
				public String add(){
					System.out.println("添加图书");
					return NONE;
				}
				public String delete(){
					System.out.println("删除图书");
					return NONE;
				}
	
	2. 通配符的访问方式:(访问的路径和方法的名称必须要有某种联系.)	通配符就是 * 代表任意的字符
		* 使用通配符的方式可以简化配置文件的代码编写，而且扩展和维护比较容易。
		* 具体实例如下：
			* 页面代码
				<a href="${pageContext.request.contextPath}/order_add.action">添加订单</a>
				<a href="${pageContext.request.contextPath}/order_delete.action">删除订单</a>
			
			* 配置文件代码
				* <action name="order_*" class="cn.itcast.demo2.OrderAction" method="{1}"></action>
			
			* Action的代码
				public String add(){
					System.out.println("添加订单");
					return NONE;
				}
				public String delete(){
					System.out.println("删除订单");
					return NONE;
				}
			
		* 具体理解：在JSP页面发送请求，http://localhost/struts2_01/order_add.action，配置文件中的order_*可以匹配该请求，*就相当于变成了add，method属性的值使用{1}来代替，{1}就表示的是第一个*号的位置！！所以method的值就等于了add，那么就找到Action类中的add方法，那么add方法就执行了！
	
	3. 动态方法访问的方式（有的开发中也会使用这种方式）
		* 如果想完成动态方法访问的方式，需要开启一个常量，struts.enable.DynamicMethodInvocation = false，把值设置成true。
			* 注意：不同的Struts2框架的版本，该常量的值不一定是true或者false，需要自己来看一下。如果是false，需要自己开启。
			* 在struts.xml中开启该常量。
				* <constant name="struts.enable.DynamicMethodInvocation" value="true"></constant>
		
		* 具体代码如下
			* 页面的代码
				* <a href="${pageContext.request.contextPath}/product!add.action">添加商品</a>
				* <a href="${pageContext.request.contextPath}/product!delete.action">删除商品</a>
			
			* 配置文件代码
				* <action name="product" class="cn.itcast.demo2.ProductAction"></action>
			
			* Action的类的代码
				public class ProductAction extends ActionSupport{
					public String add(){
						System.out.println("添加订单");
						return NONE;
					}
					public String delete(){
						System.out.println("删除订单");
						return NONE;
					}
				}
	
----------
	
                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          