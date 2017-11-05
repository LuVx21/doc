---
title: MySQL 游标
data: 2016-07-04
tags:
- MySQL
---


## 创建

```sql
declare cur cursor for select name,count from store where name = 'iphone';
```


## 遍历

```sql
declare continue handler for sqlstate '02000' set _done = 1;
declare continue handler for not found set done = true;
```

**方式1**

`loop..end loop;`

```sql
open cur;
read_loop:loop
    fetch cur into n,c;
    if done then
        leave read_loop;    --跳出游标循环
    end if;
    ......;
end loop;
close cur;
```


**方式2**

`while...do...end while;`

```sql
open cur;
fetch cur into n,c;
while(not done) do
        ......;
        fetch cur into n,c;
end while;

close cur;
```


**方式3**

`repeat...untile end repeat;`

```sql
open cur;
repeat
fetch cur into n,c;
if not done then
    ......;
end if;
until done end repeat;
close cur;
```

# 动态SQL

```sql
set @sqlStr='select * from table where condition1 = ?';
prepare s1 for @sqlStr;
--如果有多个参数用逗号分隔
execute s1 using @condition1;
--手工释放，或者是 connection 关闭时， server 自动回收
deallocate prepare s1;
```