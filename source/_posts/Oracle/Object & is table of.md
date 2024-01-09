---
title: is table of
date: 2017-04-01
tags:
- Oracle
---


<!-- TOC -->


<!-- /TOC -->


```sql
-- 创建scheme级别的type
create or replace type type_renxie_object as object
(
    aaaa varchar2(20),
    bbbb varchar2(20)
)
/

create or replace type type_renxie_table is table of type_renxie_object
/
```

```sql
set serveroutput on
set timing on

declare

    -- rec type_renxie_table := type_renxie_table();
    rec type_renxie_table;

begin
    -- 初始化内部类型
    rec := type_renxie_table();
    rec.extend;
    -- 初始化scheme类型
    -- schema级的初始化需要额外的操作
    rec(1) := type_renxie_object(null,null);
    
    rec(1).aaaa := 'aaaa';
    rec(1).bbbb := 'bbbb';
    rec.extend;
    rec(2) := type_renxie_object('aaaa2','bbbb2');

    dbms_output.put_line(rec(1).aaaa||'-'||rec(1).bbbb);
    dbms_output.put_line(rec(2).aaaa||'-'||rec(2).bbbb);

end;
/
```
缺少第二次的初始化(创建对象),会出现`ORA-06530:未初始化的组合`错误.