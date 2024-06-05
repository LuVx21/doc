<details>
<summary>点击展开目录</summary>

- [MySQL 运维](#mysql-运维)

</details>


## MySQL 运维

**所有数据库各自占用大小**

```sql
select table_schema,
       concat(truncate(sum(data_length) / 1024 / 1024, 2), 'mb')  as data_size,
       concat(truncate(sum(index_length) / 1024 / 1024, 2), 'mb') as index_size
from information_schema.tables
group by table_schema
;
```

**单个数据库占用大小**

```sql
select concat(truncate(sum(data_length) / 1024 / 1024, 2), 'mb')     as data_size,
       concat(truncate(sum(max_data_length) / 1024 / 1024, 2), 'mb') as max_data_size,
       concat(truncate(sum(data_free) / 1024 / 1024, 2), 'mb')       as data_free,
       concat(truncate(sum(index_length) / 1024 / 1024, 2), 'mb')    as index_size
from information_schema.tables
where table_schema = '数据库名'
;
```

**单个数据库所有表各自占用大小**

```sql
select table_name,
       concat(truncate(data_length / 1024 / 1024, 2), 'mb')  as data_size,
       concat(truncate(index_length / 1024 / 1024, 2), 'mb') as index_size
from information_schema.tables
where table_schema = '数据库名'
group by table_name
;
```

**单个数据库指定表占用大小**

```sql
select concat(truncate(sum(data_length) / 1024 / 1024, 2), 'mb')     as data_size,
       concat(truncate(sum(max_data_length) / 1024 / 1024, 2), 'mb') as max_data_size,
       concat(truncate(sum(data_free) / 1024 / 1024, 2), 'mb')       as data_free,
       concat(truncate(sum(index_length) / 1024 / 1024, 2), 'mb')    as index_size
from information_schema.tables
where table_name = '表名'
;
```