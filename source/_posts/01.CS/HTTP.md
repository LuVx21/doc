---
title: HTTP协议
date: 2016-04-16
tags:
- HTTP
---
<!-- TOC -->

- [关于](#关于)
- [Request](#request)
- [Response](#response)
- [请求方式](#请求方式)
    - [Get与POST的区别](#get与post的区别)
- [Cookie&Session](#cookiesession)
    - [Cookie](#cookie)
    - [Session](#session)
- [参考](#参考)

<!-- /TOC -->

# 关于

Http是无状态协议,所谓无状态可以简单理解为,浏览器发出请求,服务器作出响应,这之后便谁也不认识谁,
针对来自同一个客户端的两次请求,服务端不能判断出是不是同一客户端.

URL的格式:
```
协议名://主机名或IP[:端口号]/path/**[?key=value][#锚点]
```

# Request

首行+请求头+空行+请求体
```xml
GET <url> Http/1.1
Referer:
Accept: text/html
Accept-Language: en-us
Context-Type:
User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0; CIBA; .NET CLR 2.0.50727; .NET CLR 3.0.4506.2152;)
Accept-Encoding: gzip, deflate
Accept-Charset: utf-8
Host: http://luvx.xyz
Connection:keep-alive
Content-Length: 80
Cookie:

请求体
```

> 首行内容:请求方式 + 请求URI + 协议及其版本
> 方式是GET时,请求体为空

# Response


首行+响应头+空行+响应体
```xml
HTTP/1.1 200 OK
Location: http://luvx.xyz
Context-Type: text/html; charset=utf-8
Content-Length: 19847
Content-Encoding：gzip
Server: Microsoft-IIS/7.5
Connection: keep-alive

响应体
```
> 首行内容:协议及其版本 + 状态码 + 原因短语
> Connection: keep-alive和Http协议的无状态:HTTP是一个无状态的面向连接的协议，无状态不代表HTTP不能保持TCP连接，更不能代表HTTP使用的是UDP协议（无连接）
> HTTP/1.1起，默认都开启了Keep-Alive，保持连接特性

响应码:
* 1××:请求处理中,请求已被接受,正在处理
* 2××:请求成功,请求被成功处理
* 3××:重定向,要完成请求必须进行进一步处理
* 4××:客户端错误,请求不合法
* 5××:服务器端错误,服务器不能处理合法请求

> 200:OK
> 302:Found,重定向
> 304:Not Modified 已缓存不需要返回内容
> 400:Bad Request 客户端请求与语法错误,不能被服务器所理解
> 403:Forbidden 服务器收到请求,但是拒绝提供服务
> 500:Internal Server Error,服务器发生了不可预期的错误
> 503:Server Unavailable,服务器当前不能处理客户端的请求，一段时间后可能恢复正常

# 请求方式

基本的有4中,GET,POST,PUT 和 DELETE,其中GET和POST最常用.

## Get与POST的区别

* 功能:GET一般用来从服务器上获取资源,不会改变服务器上的资源,POST一般用来更新服务器上的资源
* REST服务:GET总是得到同样的内容,POST每次请求对资源的改变并不一定是相同的
* 请求参数形式:GET请求数据放在HTTP请求头上,以`?`分割URL和数据,以`&`连接数据;POST将数据放在HTTP请求报文的请求体中
* 安全性:POST更加安全
* 请求大小:GET请求受限于url的长度,POST则无限制

> GET请求中的数据,非英文或数字会被重新编码,这是为了避免歧义,如空格被编码为`+`,类似于中文等则被BASE64加密.
> 数据本身含有`&`,`=`会被编码为`%26`,`%3D`以和key=value中的符合区分开

# Cookie&Session

Cookie和Session都是客户端与服务器之间保持状态的解决方案.

* cookie机制采用的是在客户端保持状态的方案
* session机制采用的是在服务器端保持状态的方案

以便利店集卡兑换商品为例,首次去改便利店购买商品,店员会给你一个卡片,将上面的图片收集满可以退换商品,这就是cookie的方式.
如果是店员有个小本本,记录你集卡信息,这便是session的方式.

对比:
* `实现机制`:Session的实现常常依赖于Cookie机制,通过Cookie发送SessionID;
* `大小限制`:Cookie有大小限制并且浏览器对每个站点也有cookie的个数限制;Session没有大小限制,理论上只与服务器的内存大小有关;
* `安全性`:Cookie存在安全隐患,通过拦截或本地文件找得到cookie后可以进行攻击,而Session由于保存在服务器端,相对更加安全;
* `服务器资源消耗`:Session是保存在服务器端上会存在一段时间才会消失,如果session过多会增加服务器的压力。


## Cookie

Cookie的产生就是为了解决无状态的不便

客户端首次请求某网站,如果需要记录状态信息,网站在response的时候出了返回请求的响应外还会颁发一个cookie,客户端再次请求时,将请求和这个cookie一起发送给服务器,服务器读取Cookie,从而确认状态.

* 客户端发送一个http请求到服务器端
* 服务器端发送一个http响应到客户端,其中包含Set-Cookie头部
* 客户端发送一个http请求到服务器端,其中包含Cookie头部
* 服务器端发送一个http响应到客户端


以登录为例
在浏览器第一次访问时,发送请求,将包含账户密码等信息的表单发送到服务器.服务器返回响应
第二次访问时,发送请求和Cookie到服务器,服务器返回响应

这也是常用的自动登录功能的实现原理

![](./img/Cookie.png)
Cookie机制采用的是在客户端保持状态,即信息的保存在客户端.

名字,值,过期时间,路径和域

Cookie的作用范围:域+路径
过期时间:
不指定则为浏览器会话期间,这种会话Cookie一般不保存在磁盘上,而是在内存中,而指定了过期时间的Cookie则保存在磁盘上,直到过期为止
为0代表删除,为负数代表为会话Cookie

> Cookie具有不可跨域名性
> /sessionWeb/:路径最后必须为'/'
> .google.com:域最前必须为'.'

通常不同的浏览器进程间共享Cookie,具体怎样支持取决于浏览器厂商


```Java
HttpServletResponse.addCookie()
HttpServletRequest.getCookies()
```

## Session

Session机制采用的是在服务器端保持状态,但也会在客户端留下一个标识,
所以session中可能会使用Cookie来识别是否为同一用户

服务器会为客户端的请求创建Session,但创建之前通常检查客户端的请求总是否包含Session标识,没有才会创建,否则说明已经为该客户端创建了Session,会继续使用这个Session.在响应时会把这个Session标识返回客户端保存.
实际上并不是发出请求后,服务器就会创建Session,而是在`HttpServletRequest.getSession(true)`类似于这样的语句执行时创建

客户端将sessionid发送给服务端:Cookie方式,URL重写方式(浏览器禁用Cookie时)


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
|String getId()|返回Session的ID。该ID由服务器自动创建,不会重复|
|long getCreationTime()|返回Session的创建日期。返回类型为long,常被转化为Date类型,例如:Date createTime = new Date(session.get CreationTime())|
|long getLastAccessedTime()|返回Session的最后活跃时间。返回类型为long|
|int getMaxInactiveInterval()|返回Session的超时时间。单位为秒。超过该时间没有访问,服务器认为该Session失效|
|void setMaxInactiveInterval(int second)|设置Session的超时时间。单位为秒|
|void putValue(String attribute, Object value)|不推荐的方法。已经被setAttribute(String attribute, Object Value)替代|
|Object getValue(String attribute)|不被推荐的方法。已经被getAttribute(String attr)替代|
|boolean isNew()|返回该Session是否是新创建的|
|void invalidate()|使该Session失效|

# 参考

[Cookie&Session](https://my.oschina.net/xianggao/blog/395675?fromerr=GC9KVenE)
[图解 HTTP：Web开发相关的一些核心基础概念](https://blog.csdn.net/justloveyou_/article/details/72803200)