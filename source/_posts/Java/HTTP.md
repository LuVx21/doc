---
title: HTTP协议
date: 2016-04-16
tags:
- HTTP
---
<!-- TOC -->

- [Request](#request)
- [Response](#response)
- [Cookie](#cookie)
- [Session](#session)
- [参考](#参考)

<!-- /TOC -->


# Request

首行+请求头+空格+请求体
```xml
GET <url> Http/1.1
Accept
Accept-Language
User-Agent
Accept-Encoding
Host
Connection
Cookie


```

# Response


# Cookie

Http是无状态协议,所谓无状态可以简单理解为,浏览器发出请求,服务器作出响应,这之后便谁也不认识谁.
Cookie的产生就是为了解决无状态的不便

名字,值,过期时间,路径和域

Cookie的作用范围:域+路径
过期时间:
不指定则为浏览器会话期间,这种会话Cookie一般不保存在磁盘上,而是在内存中,而指定了过期时间的Cookie则保存在磁盘上,直到过期为止
为0代表删除,为负数代表为会话Cookie

> Cookie具有不可跨域名性
> /sessionWeb/:路径最后必须为'/'
> .google.com:域最前必须为'.'

通常不同的浏览器进程间共享Cookie,具体怎样支持取决于浏览器厂商

* 客户端发送一个http请求到服务器端
* 服务器端发送一个http响应到客户端，其中包含Set-Cookie头部
* 客户端发送一个http请求到服务器端，其中包含Cookie头部
* 服务器端发送一个http响应到客户端
![](./img/Cookie.png)

以登录为例
在浏览器第一次访问时,发送请求,将包含账户密码等信息的表单发送到服务器.服务器返回响应
第二次访问时,发送请求和Cookie到服务器,服务器返回响应

这也是常用的自动登录功能的实现原理

Cookie机制采用的是在客户端保持状态,即信息的保存在客户端.


# Session

Session机制采用的是在服务器端保持状态,但也会在客户端留下一个标识,
所以session中可能会使用Cookie来识别是否为同一用户

服务器会为客户端的请求创建Session,但创建之前通常检查客户端的请求总是否包含Session标识,没有才会创建,否则说明已经为该客户端创建了Session,会继续使用这个Session.在响应时会把这个Session标识返回客户端保存.
实际上并不是发出请求后,服务器就会创建Session,而是在`HttpServletRequest.getSession(true)`类似于这样的语句执行时创建

浏览器的关闭不会导致删除Session,因此服务器需要为Session设置一个过期时间
* 显示的关闭Session:invalidate()
* 超时:setMaxInactiveInterval(longinterval)
* 服务器进程停止
这3中情形下会删除Session

|方法|说明|
|:---|:---|
|void setAttribute(String attribute, Object value)|设置Session属性。value参数可以为任何Java Object。通常为Java Bean。value信息不宜过大|
|String getAttribute(String attribute)|返回Session属性|
|Enumeration getAttributeNames()|返回Session中存在的属性名|
|void removeAttribute(String attribute)|移除Session属性|
|String getId()|返回Session的ID。该ID由服务器自动创建，不会重复|
|long getCreationTime()|返回Session的创建日期。返回类型为long，常被转化为Date类型，例如：Date createTime = new Date(session.get CreationTime())|
|long getLastAccessedTime()|返回Session的最后活跃时间。返回类型为long|
|int getMaxInactiveInterval()|返回Session的超时时间。单位为秒。超过该时间没有访问，服务器认为该Session失效|
|void setMaxInactiveInterval(int second)|设置Session的超时时间。单位为秒|
|void putValue(String attribute, Object value)|不推荐的方法。已经被setAttribute(String attribute, Object Value)替代|
|Object getValue(String attribute)|不被推荐的方法。已经被getAttribute(String attr)替代|
|boolean isNew()|返回该Session是否是新创建的|
|void invalidate()|使该Session失效|


# 参考

[Cookie&Session](https://my.oschina.net/xianggao/blog/395675?fromerr=GC9KVenE)