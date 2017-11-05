---
title: MySQL常用命令
tags:
- MySQL
---



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


备份指定数据库

```shell
mysqldump -h localhost -u root -p <dbname> <tablename> [--add-drop-table] > e:\mysql\mydb.sql
```

--add-drop-table:只备份表结构