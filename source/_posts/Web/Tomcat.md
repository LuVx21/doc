---
title: Tomcat架构
date: 2017-12-26
tags:
- Java
---

<!-- TOC -->

- [架构组成](#架构组成)
    - [Connector & Container](#connector--container)
- [Q&A](#qa)
- [参考](#参考)

<!-- /TOC -->
# 架构组成

* Server:
* Service:对外提供服务
* Connector:用于处理连接相关的功能,并提供Socket与Request和Response相关的转化;
* Container:用于封装和管理Servlet,以及具体处理Request请求;

1个Server有多个Service,1个Service有多个Connector和1个Container

* 请求
* Service
* Connector
* 封装成Request和Response
* Container处理
* 处理结果返回给Connector
* 使用Socket将处理结果返回客户端

## Connector & Container

Socket来进行连接
Request和Response是按照HTTP协议来封装
需要实现TCP/IP协议和HTTP协议

# Q&A

如何优化Tomcat,常见的优化方式有哪些;

# 参考

1. [Tomcat](http://blog.csdn.net/xlgen157387/article/details/79006434)

