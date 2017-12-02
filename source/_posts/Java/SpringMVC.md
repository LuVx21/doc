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
