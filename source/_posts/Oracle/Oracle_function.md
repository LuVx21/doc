---
title: Procedure & Function 区别
date: 2017-03-15
tags:
- Oracle
---



```sql
create or replace function aFun(
    aaa in number,
    bbb in number,
    ccc out number
)return number
as
begin
    ccc := aaa + bbb + 1;
    return aaa + bbb;
end;
/
```
```sql
create or replace procedure aPro(
    aaa in number,
    bbb in number,
    ccc out number
)
as
begin
    ccc := aaa + bbb;
end;
/
```
```sql
set serveroutput on;
set timing on;

declare
    result number := 0;
    returnvalue number := 0;
begin

aPro(1,2,result);
dbms_output.put_line('OUT:' || result);

returnvalue := aFun(1,2,result);
dbms_output.put_line('OUT:' || result);
dbms_output.put_line('RETURN:' || returnvalue);

end;
/
```