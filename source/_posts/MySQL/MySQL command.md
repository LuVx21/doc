---
title: MySQL常用命令
date: 2016-07-04
tags:
- MySQL
---
<!-- TOC -->

- [重置mysql密码](#重置mysql密码)
- [备份](#备份)
- [恢复](#恢复)
- [记录](#记录)
- [执行文件](#执行文件)

<!-- /TOC -->

# 重置mysql密码

1. 关闭mysql服务
2. cd /usr/local/mysql/bin/
3. 登录管理员权限 sudo su
4. 禁止mysql验证功能 ./mysqld_safe --skip-grant-tables &
5. 此时服务状态为running
6. ./mysql
7. FLUSH PRIVILEGES; 
8. set password for 'root'@'localhost'=1121


重命名表

```
alter table t1 rename t2;
```

添加索引

```
alter table tablename add index index_name (name);
```

删除索引

```
alter table tablename drop index index_name;
```

增加列

```
alter table table_name add field_name field_type;
```
修改列

```
alter table table_name change old_field_name new_field_name field_type;
```
删除列

```
alter table table_name drop field_name;
```

# 备份

备份指定数据库

```shell
# 备份指定数据库的指定表
mysqldump -h localhost -u root -p <dbname> <table1> <table2> [-d|t] > dump.sql
```
> -d:仅表结构
> -t:仅数据
> --ignore-table=database_name.table_name1:设置不备份的表

# 恢复


# 记录

实现与Oracle中的`spool`相似的功能

```shell
# 方式1
mysql -u root -p --tee=/mysql/log.log
# 方式2
tee log.log
notee
# 方式3
\T log.log
\t
```

# 执行文件

```shell
# mysql –u root –p123456 -Dtest</home/user/1.sql
\. <path>
source <path>
```