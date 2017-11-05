---
title: MySQL 函数
date: 2016-07-04
tags:
- MySQL
---


```sql
drop function if exists `deletebyid`;
delimiter ;;
create function deletebyid(uid smallint unsigned) 
returns varchar(20) 
begin
    ......;
    return ..;
end
;;
delimiter ;

-- 调用函数
select <functionname>
```
