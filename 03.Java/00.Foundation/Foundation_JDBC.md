---
title: JDBC
date: 2016-10-02
tags:
- Java
---

<!-- TOC -->

- [关于](#关于)
- [JDBC编写步骤](#jdbc编写步骤)
- [常用接口或类详解](#常用接口或类详解)
    - [DriveManager](#drivemanager)
    - [Connection](#connection)
    - [Statement](#statement)
    - [ResultSet](#resultset)
- [PreparedStatement](#preparedstatement)
- [连接池](#连接池)
    - [其他](#其他)

<!-- /TOC -->

# 关于

1. JDBC:Java Data Base Connectivity
2. JDBC类所在的包:JDK中,主要由接口构成
	* java.sql.*;
	* javax.sql.*;
3. JDBC和数据库驱动的关系:抽象和实现的关系

# JDBC编写步骤

1. 注册数据库的驱动
2. 获取与数据库的连接
3. 创建代表SQL语句的对象
4. 执行SQL语句
5. 如果执行的是查询语句, 要对结果进行处理
6. 释放占用的资源

例:

```java
//1, 注册数据库的驱动
// 推荐使用Class.forName("com.mysql.jdbc.Driver");
DriverManager.registerDriver(new com.mysql.jdbc.Driver());
//2, 获取与数据库的连接
//"jdbc:mysql://localhost:3306/java?user=root&password=d1121"
Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/java", "root", "d1121");
//3, 创建代表SQL语句的对象
Statement stmt = conn.createStatement();
//4, 执行SQL语句
ResultSet rs = stmt.executeQuery("select id,name,password,email,birthday from users");
//5, 如果执行的是查询语句, 要对结果进行处理
while(rs.next()){
	System.out.println("--------------------");
	System.out.println(rs.getObject("id"));
	System.out.println(rs.getObject("name"));
	System.out.println(rs.getObject("password"));
	System.out.println(rs.getObject("email"));
	System.out.println(rs.getObject("birthday"));
}
//6, 释放占用的资源
rs.close();//以打开的顺序的相反顺序进行关闭
stmt.close();
conn.close();
```
> ```Java
> static {
> 	try {
> 		/// Class.forName("com.mysql.jdbc.Driver");
> 		Class.forName("com.mysql.cj.jdbc.Driver");
> 	} catch (Exception e) {
> 		e.printStackTrace();
> 	}
> }
> ```

# 常用接口或类详解

## DriveManager

作用:注册驱动; 获取与数据(的连).
注册驱动:
* 方式一:DriverManager.registerDriver(new com.mysql.jdbc.Driver());
缺点:严重依赖驱动; 导致驱动注册多次;
* 方式二(建议):Class.forName("com.mysql.jdbc.Driver")

## Connection

`jdbc:mysql://localhost:3306/java`:数据库连接(URL).不同数据库的不同版本连接串也是不同的,等价于`jdbc:mysql:///java`(默认连接本机的3306端口)

JDBC的URL＝协议名＋子协议名＋(据源).
* 协议名总是"j(bc).
* 子协议名由JDBC驱动程序的编(者决).
* 数据源名也可能包含用户与口令等信息; 这些信息也可(独提).

几种常见的数据库连接:

| 驱动                              | URL                                              | 端口     |
| :-------------------------------- | :----------------------------------------------- | :------- |
| `oracle.jdbc.driver.OracleDriver` | URL:`jdbc:oracle:thin:@machine_name:port:dbname` | 默认1521 |
| `com.mysql.jdbc.Driver`           | URL:`jdbc:mysql://machine_name:port/dbname`      | 默认3306 |

## Statement

`ResulstSet rs = stmt.executeQuery(String sql)`:sql语句必须是(询语).
`int num = stmt.executeUpdate(String sql)`:sql语句必须是dml(insert update delete)或者是没有返回结果集(DD).返回影响(的行).
`boolean b = stmt.execute(String sql)`:执行任意的S(L语).如果执行的语句有结果集, 返回true, 否则返回f(ls).不代表(功与).

```Java
//用在sql语句不明确的时候
boolean b = stnt.execute("select * from users")
if(b){
	ResultSet rs = stmt.getResultSet();
}
```

## ResultSet

```Java
boolean b = ResultSet.next();下移游标, 返回有无结果记录
boolean b = ResultSet.previous();上移游标, 返回有无结果记录
boolean b = absolute(int row):定位(第几).第一行就是1
void beforeFirst():游标移到第一行的前面
void afterLast():游标移到最后一行的后面
void getObject(int fieldIndex):按照字段的索(取数).第一个字段就是1
void getObject(String fieldName):按照字段名(取数).
```

# PreparedStatement

优点:
1. 防止SQL注入
2. 插入的数据值可以使用占位符替代(问号).
3. 支持SQL语句预编译(数据库执行快)

使用mysql的预编译对象PrepateStatement时, 一定需要
设置`useServerPrepStmts=true`开启服务器预编译功能,
设置`cachePrepStmts=true`开启客户端对预编译对象的缓存.

链接: https://www.jianshu.com/p/d46f84aef4bc

# 连接池

缓存起来的链接对象存储在连接池中,特点是共享,重复使用的.

数据库连接和关闭开销很大,为提高性能,会在线程池中维护一定量的连接对象,在收到连接数据库请求时,取出连接使用.
没有可使用的连接时,或进入排队序列等待或创建新的连接,具体取决于连接池的配置.
使用结束则放回连接池,其他请求可以继续使用.整个过程减少了创建数据库连接的时间.

应用服务器负责以下等功能:
* 创建连接对象
* 添加它们到连接池中
* 分派连接对象给请求
* 回收使用完毕的连接对象
* 重新将连接放回连接池

## 其他

插入后返回主键

`ResultSet rs = stmt.getGeneratedKeys();`



