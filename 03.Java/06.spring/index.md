<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [概念](#概念)
- [结构](#结构)
- [特点](#特点)
- [QA](#qa)

<!-- /TOC -->
</details>


## 概念

Spring 组件化技术
所有的类都可以看做是一个组件, 基于ioc交给容器, 而后直接使用这个组件, 其中最突出的就是Bean

```Java
@NoArgsConstructor
@Getter
@Setter
public class Ren implements Serializable {
    private String name;
}
```

可以看到, 一个标准的bean符合以下条件:
1. 无参构造方法
2. private属性
3. public暴露属性
4. 可序列化

再广义一些, 所有的组件都可以被称为bean

## 结构

![](https://gitee.com/LuVx/img/raw/master/spring-overview.png)

自身组件:

* WEB层:Spring MVC
* 业务层:Bean管理:(IOC)
* 持久层:Spring的JDBC模板.ORM模板用于整合其他的持久层框架

## 特点

* 方便解耦, 简化开发
    * Spring就是一个大工厂, 可以将所有对象创建和依赖关系维护, 交给Spring管理
* AOP编程的支持
    * Spring提供面向切面编程, 可以方便的实现对程序进行权限拦截, 运行监控等功能
* 声明式事务的支持
    * 只需要通过配置就可以完成对事务的管理, 而无需手动编程
* 方便程序的测试
    * Spring对Junit4支持, 可以通过注解方便的测试Spring程序
* 方便集成各种优秀框架
    * Spring不排斥各种优秀的开源框架, 其内部提供了对各种优秀框架(如:Struts2, Hibernate, MyBatis, Quartz等)的直接支持
* 降低JavaEE API的使用难度
    * Spring 对JavaEE开发中非常难用的一些API(JDBC, JavaMail, 远程调用等), 都提供了封装, 使这些API应用难度大大降低

## QA

**spring可以为我们解决什么问题**

业务解耦

**springMVC在一个请求过来是怎么找到相应的处理器**

简单回答即可, 例如所有请求都会被DispatcherServlet拦截->通过request找到handler->通过handler找到HandlerAdapter



