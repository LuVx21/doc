---
title: HTTP协议
date: 2016-04-16
tags:
- HTTP
---
<!-- TOC -->

- [关于](#关于)
- [Request](#request)
    - [请求方式](#请求方式)
- [Response](#response)
    - [Get与POST的区别](#get与post的区别)
- [Cookie&Session](#cookiesession)
    - [Cookie](#cookie)
    - [Session](#session)
- [缓存](#缓存)
    - [缓存验证](#缓存验证)
- [虚拟主机](#虚拟主机)
- [HTTPS](#https)
- [Web安全](#web安全)
- [HTTP/1.0和HTTP/1.1区别](#http10和http11区别)
- [HTTP/1.1和HTTP/2.0区别](#http11和http20区别)
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
Host: http://luvx.xyz
Referer:
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us
Context-Type:
User-Agent: Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0; CIBA; .NET CLR 2.0.50727; .NET CLR 3.0.4506.2152;)
Accept-Encoding: gzip,deflate
Accept-Charset: utf-8
Keep-Alive: 300
Connection:keep-alive
Content-Length: 80
If-Modified-Since: Mon, 25 May 2016 03:19:18 GMT
Cookie:

请求体
```

> 首行内容:请求方式 + 请求URI + 协议及其版本
> 方式是GET时,请求体为空

## 请求方式

* GET:获取资源
* POST:传输实体,用于提交或更改资源
* HEAD:获取报文首部
* PUT:修改文件,无验证机制,有安全问题
* PATCH:部分修改资源
* DELETE:删除文件
* OPTIONS:查询支持的方法
* CONNECT:要求使用隧道协议连接代理,要求在与代理服务器通信时建立隧道,使用 SSL和 TLS协议把通信内容加密后经网络隧道传输.
* TRACE:追踪路径

# Response


首行+响应头+空行+响应体
```xml
HTTP/1.1 200 OK
Location: http://luvx.xyz
Content-Type: text/html; charset=utf-8
Content-Length: 19847
Content-Encoding：gzip
Server: Microsoft-IIS/7.5
Cache-Control: private, max-age=30
Expires: Mon, 25 May 2016 03:20:33 GMT
Last-Modified: Mon, 25 May 2016 03:20:03 GMT
Vary: Accept-Encoding
Date: Mon, 25 May 2016 03:20:02 GMT
Connection: keep-alive

响应体
```
> 首行内容:协议及其版本 + 状态码 + 原因短语
> Connection: keep-alive和Http协议的无状态:HTTP是一个无状态的面向连接的协议,无状态不代表HTTP不能保持TCP连接,更不能代表HTTP使用的是UDP协议（无连接）
> HTTP/1.1起,默认都开启了Keep-Alive,保持连接特性,从而维持长连接(建立一次连接,能进行多次HTTP通信)

响应码:
* 1××:请求处理中,请求已被接受,正在处理
* 2××:请求成功,请求被成功处理
    * 200 OK,表示从客户端发来的请求在服务器端被正确处理
    * 204 No content,表示请求成功,但响应报文不含实体的主体部分
    * 206 Partial Content,进行范围请求
* 3××:重定向,要完成请求必须进行进一步处理
    * 301 moved permanently,永久性重定向,表示资源已被分配了新的 URL
    * 302 found,临时性重定向,表示资源临时被分配了新的 URL
    * 303 see other,表示资源存在着另一个 URL,应使用 GET 方法获取资源
    * 304 not modified,表示服务器允许访问资源,但已缓存不需要返回内容,多见于请求中含有If-Match,If-ModifiedSince,If-None-Match,If-Range,If-Unmodified-Since等
    * 307 temporary redirect,临时重定向,和302含义相同,但不要求把重定向请求的POST方法改为GET
* 4××:客户端错误,请求不合法
    * 400 bad request,客户端请求有语法错误,不能被服务器所理解
    * 401 unauthorized,表示发送的请求需要有通过 HTTP 认证的认证信息
    * 403 forbidden,表示对请求资源的访问被服务器拒绝
    * 404 not found,表示在服务器上没有找到请求的资源
    * 416 Requested Range Not Satisfiable,范围请求是请求的范围越界时返回
* 5××:服务器端错误,服务器不能处理合法请求
    * 500 internal sever error,表示服务器端在执行请求时发生了错误
    * 503 service unavailable,表明服务器暂时处于超负载或正在停机维护,无法处理请求

## Get与POST的区别

* 功能:GET一般用来从服务器上获取资源,不会改变服务器上的资源,POST一般用来更新服务器上的资源
* REST服务:GET总是得到同样的内容,POST每次请求对资源的改变并不一定是相同的
* 请求参数形式:GET请求数据放在HTTP请求头上,以`?`分割URL和数据,以`&`连接数据,只支持ASCII字符;POST将数据放在HTTP请求报文的请求体中,支持标准字符集
* 安全性:POST更加安全,POST不安全(PUT,DELETE同样)
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
* `服务器资源消耗`:Session是保存在服务器端上会存在一段时间才会消失,如果session过多会增加服务器的压力.


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
|void setAttribute(String attribute, Object value)|设置Session属性.value参数可以为任何Java Object.通常为Java Bean.value信息不宜过大|
|String getAttribute(String attribute)|返回Session属性|
|Enumeration getAttributeNames()|返回Session中存在的属性名|
|void removeAttribute(String attribute)|移除Session属性|
|String getId()|返回Session的ID.该ID由服务器自动创建,不会重复|
|long getCreationTime()|返回Session的创建日期.返回类型为long,常被转化为Date类型,例如:Date createTime = new Date(session.get CreationTime())|
|long getLastAccessedTime()|返回Session的最后活跃时间.返回类型为long|
|int getMaxInactiveInterval()|返回Session的超时时间.单位为秒.超过该时间没有访问,服务器认为该Session失效|
|void setMaxInactiveInterval(int second)|设置Session的超时时间.单位为秒|
|void putValue(String attribute, Object value)|不推荐的方法.已经被setAttribute(String attribute, Object Value)替代|
|Object getValue(String attribute)|不被推荐的方法.已经被getAttribute(String attr)替代|
|boolean isNew()|返回该Session是否是新创建的|
|void invalidate()|使该Session失效|

# 缓存

## 缓存验证

ETag:资源的唯一标识.
将缓存资源的 ETag 值放入 If-None-Match 首部,服务器接收后和该资源的ETag对比,如果一致,说明自上次被缓存依赖该资源没有更新,则响应304,告知浏览器使用缓存.

Last-Modified:
包含在服务器发送的响应报文中,代表资源的最后修改时间.但是它是一种弱校验器,因为只能精确到一秒,所以它通常作为 ETag 的备用方案.

If-Modified-Since:
包含在请求中,服务器接收后和服务器上的版本最后修改时间进行对比,若一致,则返回304,告知浏览器使用缓存

# 虚拟主机

HTTP/1.1 使用虚拟主机技术,使得一台服务器拥有多个域名,并且在逻辑上可以看成多个服务器

# HTTPS

Https是身披SSL(Secure Socket Layer)外壳的Http,运行于SSL上,SSL运行于TCP之上,是添加了加密和认证机制的HTTP,具有了加密（防窃听）、认证（防伪装）和完整性保护（防篡改）特性.
SSL:Secure Socket Layer
TSL:Transport Layer Security,其前身是SSL

HTTPS协议的工作流程:
HTTPS在传输数据之前需要客户端(浏览器)与服务端(网站)之间进行一次握手,在握手过程中将确立双方加密传输数据的密码信息.
TLS/SSL协议不仅仅是一套加密传输的协议,TLS/SSL中使用了非对称加密,对称加密以及HASH算法.

握手过程的简单描述如下：

1. 浏览器将自己支持的一套加密规则发送给网站.
2. 网站从中选出一组加密算法与HASH算法,并将自己的身份信息以证书的形式发回给浏览器.证书里面包含了网站地址,加密公钥,以及证书的颁发机构等信息.
3. 获得网站证书之后浏览器要做以下工作：
    1. 验证证书的合法性(颁发证书的机构是否合法,证书中包含的网站地址是否与正在访问的地址一致等),如果证书受信任,则浏览器栏里面会显示一个小锁头,否则会给出证书不受信的提示.
    2. 如果证书受信任,或者是用户接受了不受信的证书,浏览器会生成一串随机数的密码,并用证书中提供的公钥加密.
    3. 使用约定好的HASH计算握手消息,并使用生成的随机数对消息进行加密,最后将之前生成的所有信息发送给网站.

4. 网站接收浏览器发来的数据之后要做以下的操作：
    1. 使用自己的私钥将信息解密取出密码,使用密码解密浏览器发来的握手消息,并验证HASH是否与浏览器发来的一致.
    2. 使用密码加密一段握手消息,发送给浏览器.

5. 浏览器解密并计算握手消息的HASH,如果与服务端发来的HASH一致,此时握手过程结束,之后所有的通信数据将由之前浏览器生成的随机密码并利用对称加密算法进行加密.

这里浏览器与网站互相发送加密的握手消息并验证,目的是为了保证双方都获得了一致的密码,并且可以正常的加密解密数据.
其中非对称加密算法用于在握手过程中加密生成的密码,对称加密算法用于对真正传输的数据进行加密,而HASH算法用于验证数据的完整性.
由于浏览器生成的密码是整个数据加密的关键,因此在传输的时候使用了非对称加密算法对其加密.

二者之间存在如下不同:
端口不同:Http与Http使用不同的连接方式,用的端口也不一样,前者是80,后者是443;
资源消耗:和HTTP通信相比,Https通信会由于加减密处理消耗更多的CPU和内存资源;
开销:Https通信需要证书,而证书一般需要向认证机构购买;
Https的加密机制是一种共享密钥加密和公开密钥加密并用的混合加密机制.

# Web安全

* 跨站脚本攻击Cross-Site Scripting, XSS）,将代码注入到用户浏览的网页上，使用户记载并执行.
* 跨站请求伪造（Cross-site request forgery，CSRF）攻击者通过一些技术手段欺骗用户的浏览器去访问一个自己曾经认证过的网站并执行一些操作
    * 检查 Referer 字段
    * 添加校验 Token
* SQL注入
* 拒绝服务攻击(denial-of-service attack，DoS)

# HTTP/1.0和HTTP/1.1区别

HTTP/1.1:
* 默认是持久连接
* 支持管线化处理
* 支持虚拟主机
* 新增状态码 100
* 支持分块传输编码
* 新增缓存处理指令 max-age

# HTTP/1.1和HTTP/2.0区别

* 二进制分帧层
* 服务端推送
* 首部压缩

# 参考

[Cookie&Session](https://my.oschina.net/xianggao/blog/395675?fromerr=GC9KVenE)
[图解 HTTP：Web开发相关的一些核心基础概念](https://blog.csdn.net/justloveyou_/article/details/72803200)
[1](https://www.cnblogs.com/heluan/p/8620312.html)