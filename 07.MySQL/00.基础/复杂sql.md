<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [定位数据](#定位数据)
- [查询后更新自身](#查询后更新自身)
- [比较表是否相同](#比较表是否相同)
    - [替换换行/回车符](#替换换行回车符)
    - [delete where](#delete-where)

<!-- /TOC -->
</details>

# 定位数据

找出既没有主键也没有唯一性索引的表
```sql
select
    table_schema,
    table_name
from
    information_schema.tables
where
    (table_schema, table_name) not in (
        select distinct table_schema, table_name
        from information_schema.table_constraints
        where constraint_type in ('primary key', 'unique')
    )
    and table_schema not in ('sys', 'mysql', 'information_schema', 'performance_schema')
```
# 查询后更新自身

TODO

```sql
update user_renxie2 R0
set result =
(SELECT (user_name = password) as result
FROM `user_renxie2` R1
where R0.id1 = R1.id1);
----------------------------------
update user_renxie2
set result = R1.result
from user_renxie2 R0
inner join (
select * from (
SELECT id1 as id1,(user_name = password) as result FROM `user_renxie2`
) a
) R1 on R0.id1 = R1.id1;
---------------------------------
update user_renxie2 R0 inner join (select * from (SELECT id1, (user_name = password) as result FROM `user_renxie2`) a) R1 on R1.id1 = R0.id1
using(result)
set R0.result = R1.result;
```

可用:
```sql
update user_renxie2 R1,
(
    select * from (
        SELECT id1 as id1,(user_name = password) as result FROM `user_renxie2`
    )t
)R2
set R1.result = R2.result
where R1.id1 = R2.id1
;
```

# 比较表是否相同

```sql
select id,title
from (
    select id, title from t1
    union all
    select id,title from t2
) tbl
group by id, title
having count(*) = 1
order by id;
```
或
```sql
select * from (select
(CONCAT(R1.db_name, R1.table_name, R1.pk_field, R1.pk_value)
=
CONCAT(R2.db_name, R2.table_name, R2.pk_field, R2.pk_value))as a
from
user R1 inner join user_1 R2 on R1.db_name = R2.db_name
)t
where t.a <> 1
```

## 替换换行/回车符

```sql
-- 替换换行/回车符
update user set user_name = REPLACE(REPLACE(user_name, CHAR(10), ''), CHAR(13), '')
```

## delete where

```sql
delete R1
from t_dwh_check_result R1 
    inner join (select * from (select distinct db_name,table_name from t_dwh_check_result where result = 5)a) R2
    on R1.db_name = R2.db_name and R1.table_name = R2.table_name
where R1.result <> 5
```

