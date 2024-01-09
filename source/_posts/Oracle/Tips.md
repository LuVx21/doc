---
title: Oracle功能
date: 2017-04-05
tags:
- Oracle
---
<!-- TOC -->

- [Oralce 递归查询](#oralce-递归查询)
- [闪回查询](#闪回查询)
- [Oracle Merge](#oracle-merge)
- [Oralce 批量执行多个sql文件](#oralce-批量执行多个sql文件)
    - [Linux下](#linux下)
    - [变量比较](#变量比较)

<!-- /TOC -->

# Oralce 递归查询

@(Oracle)

> [TOC]

```
SELECT kei_kenshu_key,hktgmt_kei_kenshu_key
FROM
    tmc0110 KEIKN
WHERE
  --  KEIKN.hktgmt_kei_kenshu_key IS NULL
--AND ROWNUM = 1
KEIKN.rnri_sakujo_sgn  = '0'
START WITH
KEIKN.kei_kenshu_key
= '10000000000000845686'
CONNECT BY PRIOR
KEIKN.hktgmt_kei_kenshu_key
= KEIKN.kei_kenshu_key
;
```


# 闪回查询

@(Oracle)

```
select * from table as of timestamp sysdate-5/1440;
```

```
select * from TMC0730 as of timestamp (systimestamp - interval '1'day);
```
```
select * from test as of timestamp to_date('2012-08-29 19:49:00', 'yyyy-mm-dd hh24:mi:ss')
```

http://blog.csdn.net/cooljun618/article/details/7180401
http://docs.oracle.com/cd/B28359_01/appdev.111/b28424/adfns_flashback.htm#BJFJDBAJ


synonym
index


# Oracle Merge

@(Oracle)

> [toc]

MERGE命令,你能够在一个SQL语句中对一个表同时执行inserts和updates操作. MERGE命令从一个或多个数据源中选择行来updating或inserting到一个或多个表.在Oracle10g中MERGE有如下一些改进:

1. UPDATE或INSERT子句是可选的
2. UPDATE和INSERT子句可以加WHERE子句
3. 在ON条件中使用常量过滤谓词来insert所有的行到目标表中,不需要连接源表和目标表
4. UPDATE子句后面可以跟DELETE子句来去除一些不需要的行

语法:
```
merge into schema.table R1
using schema.{table | view |subquery } R2 -- 此处可以使用子查询
on (condition)  -- 关联条件
when matched then
    merge_update_clause / merge_delete_clause
when not matched then
    merge_insert_clause / merge_delete_clause;
```
merge into是特有的功能, 相当于在 MSSQL中的

```
if exists(...)
update table
else
Insert into table.
```

mergeinto语法不仅没有if exists语法啰嗦, 而且比if exists还要高效很多, 常用来在oracle之间同步数据库表
优点:

* 避免了分开更新
* 提高性能并易于使用
* 在数据仓库应用中十分有用
* 使用merge比传统的先判断再选择插入或更新快很多


# Oralce 批量执行多个sql文件


新建 index.sql文件
添加以下内容
```sql
@start.sql
@@执行的文件.sql
@@文件夹/执行的文件.sql
@@end.sql
```
> 执行的文件可以在多层文件夹下,写相对路径即可

start.sql文件
```
set serveroutput on
spool "log.log" append
```

end.sql文件
```
spool off
```
执行sql文件的脚本文件,run.bat

```shell
@rem ===============================================================================
@echo off
@rem ===============================================================================

@rem 设置用户
SET ORACLE_USERID=scott
@rem 指定用户密码
SET ORACLE_PASSWARD=1121
@rem 设置数据库
SET ORACLE_SID=XE

@rem 动态输入密码
@rem SET /P ORACLE_PASSWARD=input the Password of %ORACLE_USERID%:
@rem ===============================================================================

@rem 配置数据库登录信息
SET W_CONNECT=%ORACLE_USERID%/%ORACLE_PASSWARD%@%ORACLE_SID%

if "%ORACLE_PASSWARD%"=="" (
echo パスワードを入力してください:
echo off
pause
exit 2
)

echo ================Info of User=============
echo User:		%ORACLE_USERID%
echo PassWord:	%ORACLE_PASSWARD%
echo DB:		%ORACLE_SID%
echo ================Run Start================

@rem 登录数据库,执行文件
SQLPLUS %W_CONNECT% "@index.sql"

@rem 异常信息
if errorlevel 1 (
echo RenXie-ERROR!
echo off
pause
)

EXIT
echo ================Run End===============

echo off

pause
```

## Linux下

```
sqlplus -S /nolog <<EOF
    conn test/test@test
    @a.sql
    @b.sql
    @c.sql
EOF
```

## 变量比较

变量i和v是否相等

1. 当i和v都为null的时候, 认为i和v相等.
2. 当i和v中只有一个为null, 肯定不相等.
3. 当i和v都不为null的时候, 就可以用‘=’号来判断它们是否相等.

↓

```sql
i is null and v is null
or
i is not null and v is not null and i = v
```
那么两个字符串不相等的条件表达式只需要判断相等的表达式为false就可以了.

把判断两个字符串是否相等的功能写成函数:

```sql
create or replace function isequal
(
    var1 in varchar2,
    var2 in varchar2
)
return number  -- 0:不等 1:相等 -1:错误
is
    if(
        var1 is null and var2 is null
    or  var1 is not null and var2 is not null and var1 = var2
    )
    then
       return 1;
    else
       return 0;
    enf if;
begin
exception
    when others then
    return -1;
end;
```

测试程序:

```sql
declare
    var1 varchar2(10) := null;
    var2 varchar2(10) := 'a';
begin
    if(isequal(var1, var2) = 1)
    then
        dbms_output.put_line('=');
    else
        dbms_output.put_line('<>');
    end if;
end ;
```

merge into emp using () on when matched then xx when not matched then xx;