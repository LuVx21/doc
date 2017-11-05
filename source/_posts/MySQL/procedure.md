---
title: MySQL 存储过程
date: 2016-07-04
tags:
- MySQL
---

# 创建

```sql
drop procedure if exists `proc_adder`;
delimiter ;;-- 默认的输入的结束符;替换成;;
create definer=`root`@`localhost` procedure `proc_adder`(in a int, in b int, out sum int)
begin
    ......
end
;;
delimiter ;

-- 调用
set @b=5;
call proc_adder(2,@b,@s);
select @s as sum;
```

> delimiter:mysql默认的delimiter是`;`,告诉mysql解释器，该段命令是否已经结束了，mysql是否可以执行了。
> 这里使用delimiter重定义结束符的作用是不让存储过程中的语句在定义的时候输出

# 在过程中使用条件语句

```sql
if condition1 then
    ......;
elseif condition2 then
    ......;
else
    ......;
end if;
```

```sql
case type
when 0 then
    ......;
when 1 then
    ......;
else
    ......;
end case;
```

# 在过程中使用循环语句

```sql
while condition1 do
    ......;
end while;
```

> leave:跳出虚幻
> iterate:跳出本次循环

其他参见[cursor](cursor.md)