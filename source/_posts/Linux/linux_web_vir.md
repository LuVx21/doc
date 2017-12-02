---
title: Linux下web开发环境搭建
date: 2017-11-10
tags:
- Linux
---

# 安装jdk

a. 检测是否安装了jdk
```
运行java -version
```
b. 若有需要将其卸载

c. 查看安装那些jdk
```
rpm -qa | grep java
```
d. 卸载
先卸载 openjdk 1.7

```
rpm -e --nodeps 卸载的包
rpm -e --nodeps java-1.7.0-openjdk-1.7.0.45-2.4.3.3.el6.i686
```

再卸载 openjdk 1.6
```
rpm -e --nodeps java-1.6.0-openjdk-1.6.0.0-1.66.1.13.0.el6.i686
```

e. 安装jdk
```
mkdir java
cp /root/jdk.xxxxx.tar.gz /usr/local/java
tar -zxvf jdk.xxx.tar.gz
```

f. 安装依赖
```
yum install glibc.i686
```
g. 配置环境变量
	编辑  vi /etc/profile
	在文件最后添加一下信息
		#set java environment
		JAVA_HOME=/usr/local/java/jdk1.7.0_72
		CLASSPATH=.:$JAVA_HOME/lib.tools.jar
		PATH=$JAVA_HOME/bin:$PATH
		export JAVA_HOME CLASSPATH PATH
	保存退出
	source /etc/profile  使更改的配置立即生效

# 安装mysql

a.检测是否安装了mysql
	rpm  -qa | grep mysql
b.卸载系统自带的mysql
	rpm -e --nodeps 卸载的包
	rpm -e --nodeps mysql-libs-5.1.71-1.el6.i686
c.上传mysql
d.在 /usr/local/ 创建一个mysql
e.复制mysql 到 mysql目录下
f.解压 tar
	会有几个rpm文件
g.安装
	安装mysql的服务器端
		rpm -ivh MySQL-server-5.5.49-1.linux2.6.i386.rpm
		注意:第一次登录mysql的时候没有不需要密码的 以后都需要
	安装mysql的客户端
		rpm -ivh MySQL-client-5.5.49-1.linux2.6.i386.rpm
h.查看mysql的服务状态
	service mysql status
	启动 mysql
	service mysql start
	停止mysql
	service mysql stop

i.修改mysql的root的密码
	登录:mysql -uroot
	修改密码:
		use mysql;
		update user set password = password('1234') where user = 'root';
		flush privileges;# 刷新
j.开启远程访问
	grant all privileges on *.* to 'root' @'%' identified by '1234';
	flush privileges;
k.开启防火墙端口 3306 退出mysql
	3306端口放行
	/sbin/iptables -I INPUT -p tcp --dport 3306 -j ACCEPT
	将该设置添加到防火墙的规则中
	/etc/rc.d/init.d/iptables save
l:设置mysql的服务随着系统的启动而启动
	加入到系统服务：
	chkconfig --add mysql
	自动启动：
	chkconfig mysql on

# 安装tomcat

a.在/usr/local/		创建tomcat目录
b.复制tomcat 到 /usr/local/tomcat
c.解压tomcat
d.启动tomcat 进入 bin
	方式1:
		sh startup.sh
	方式2:
		./startup.sh
e.开启端口号 8080
	8080端口放行
	/sbin/iptables -I INPUT -p tcp --dport 8080 -j ACCEPT
	将该设置添加到防火墙的规则中
	/etc/rc.d/init.d/iptables save

注意:
	查看日志文件
		tail -f logs/catalina.out
	退出 ctrl+c

# 发布项目

a.数据库和表
	备份store28的数据库
	在windows下
		mysqldump -uroot -p1234 store28 > g:/1.sql
	将1.sql上传 root目录
	通过远程工具还原数据库
		先登录mysql
		创建数据库 store28
		进入store28
			source /root/1.sql

b.项目
	将项目打包 war
	war包的特点:
		在tomcat/webapps目录下 只要tomcat启动 war会自动解压
	将store.war上传到虚拟机的root目录下
	将store.war移动到tomcat/webapps下即可
