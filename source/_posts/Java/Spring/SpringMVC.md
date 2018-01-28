---
title: SpringMVC
date: 2017-10-03
tags:
- Java
- SpringMVC
---

# 处理流程


# 

核心组件:

* 处理器映射器
* 处理器适配器
* 视图解析器


# 参数绑定(从请求中接收参数)

1. 默认类型:
    在controller方法中可以有也可以没有,看自己需求随意添加.
    httpservletRqeust,httpServletResponse,httpSession,Model(ModelMap其实就是Mode的一个子类
    ,一般用的不多)
2. 基本类型:string,double,float,integer,long.boolean
3. pojo类型:页面上input框的name属性值必须要等于pojo的属性名称
4. vo类型:页面上input框的name属性值必须要等于vo中的属性.属性.属性....
5. 自定义转换器converter:
    作用:由于springMvc无法将string自动转换成date所以需要自己手动编写类型转换器
    需要编写一个类实现Converter接口
    在springMvc.xml中配置自定义转换器
    在springMvc.xml中将自定义转换器配置到注解驱动上

# 常用注解

`@Controller`
负责注册一个bean 到spring 上下文中.

`@RequestMapping`
注解为控制器指定可以处理哪些 URL 请求.

`@RequestBody`
该注解用于读取Request请求的body部分数据,使用系统默认配置的HttpMessageConverter进行解析,然后把相应的数据绑定到要返回的对象上 ,再把HttpMessageConverter返回的对象数据绑定到 controller中方法的参数上.

`@ResponseBody`
该注解用于将Controller的方法返回的对象,通过适当的HttpMessageConverter转换为指定格式后,写入到Response对象的body数据区.

`@ModelAttribute`
* 在方法定义上使用:Spring MVC 在调用目标处理方法前,会先逐个调用在方法上标注了@ModelAttribute 的方法.
* 在方法的形参前使用:可以从隐含对象中获取隐含的模型数据中获取对象,再将请求参数–绑定到对象中,再传入入参将方法入参对象添加到模型中.

`@RequestParam`
在处理方法入参处使用 @RequestParam 可以把请求参数传递给请求方法.

`@PathVariable`
绑定 URL 占位符到入参.

`@ExceptionHandler`
注解到方法上,出现异常时会执行该方法.

`@ControllerAdvice`
使一个Contoller成为全局的异常处理类,类中用@ExceptionHandler方法注解的方法可以处理所有Controller发生的异常.