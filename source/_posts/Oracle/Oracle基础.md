---
title: Oracle基础总结
date: 2017-03-01
tags:
- Oracle
---

<!-- TOC -->

<!-- /TOC -->

# %Type

使一个变量的数据类型与另一个已经定义了的变量（尤其是表的某一列）的数据类型相一致,不需要确切知道被参照的变量的数据类型.

# %Rowtype

`%ROWTYPE`来定义一个表示表中一行记录的变量,一行记录可以保存从一个表或游标中查询到的整个数据行的各列数据.各列数据类型都相同.

# Recode

由`单行多列`的标量构成的复合结构。可以看做是一种用户自定义数据类型。组成类似于多维数组,个人看作一个单行的小表
可以直接赋值, 不可以整体比较,不可以整体判断为空

```sql
type  type_recode  is record
(
    filed1 table.filed%type [not null] [：=expr1],
    filedn table.filed%type [not null] [：=exprn]
);
v_recode type_recode;
```

# Varray

数组是具有相同数据类型的一组成员的集合
```
type varray_name varray(size) of element_type [not null];.
array varry_name := varry_name('1','2','3'); -- 初始化赋值
```

# Table

记录表（或索引表）数据类型,可以处理多行记录.关键字INDEX BY表示创建一个主键索引，以便引用记录表变量中的特定行
**binary_integer**
其作用是,加了”index bybinary_integer”后，numbers类型的下标就是自增长，numbers类型在插入元素时，不需要初始化，不需要每次extend增加一个空间。
而如果没有这句话“indexby binary_integer”，那就得要显示对初始化，且每插入一个元素到numbers类型的table中时，都需要先extend

```sql
type table_name is table of element_type [not null]
index by [binary_integer|pls_integer|varray2];
v_name table_name := table_name('1','2','3'); -- 初始化赋值
```

`is table` 后可以是`rowtype`,也可以是`recode`

`VARRAY`和`TABLE`集合不能直接对其进行`查询`。只能对其进行`遍历`。

# bulk collect(批量聚合类型)

通过bulk collect减少loop处理的开销，使用Bulk Collect提高Oracle查询效率

可以让我们在PL/SQL中能使用批查询，批查询在某些情况下能显著提高查询效率。
采用bulk collect可以将查询结果一次性地加载到collections中。
而不是通过cursor一条一条地处理。
可以在select into,fetch into,returning into语句使用bulk collect。
注意在使用bulk collect时，所有的into变量都必须是collections

```sql
set serveroutput on

declare
TYPE emp_table_type IS TABLE OF emp%ROWTYPE INDEX BY BINARY_INTEGER;
v_emp_table emp_table_type;
begin
SELECT * BULK COLLECT INTO v_emp_table FROM emp WHERE deptno=&deptno;
FOR i IN 1..v_emp_table.COUNT LOOP
      dbms_output.put_line('EMPLOYEE_INFO:'||v_emp_table(i).ename||
                                        ','||v_emp_table(i).job||
                                        ','||v_emp_table(i).hiredate);
END LOOP;
end;
/
```

上述:

- INDEX BY BINARY_INTEGER 指索引组织类型
- BULK COLLECT INTO 指是一个成批聚合类型, 简单的来说 , 它可以存储一个多行多列存储类型

```sql
set serveroutput on

--在returning into中使用bulk collect
CREATE TABLE emp2 AS SELECT * FROM emp;
DECLARE
TYPE NumList IS TABLE OF emp.empno%TYPE;
enums NumList;
TYPE NameList IS TABLE OF emp.ename%TYPE;
names NameList;
BEGIN

DELETE FROM emp2 WHERE deptno = 30
RETURNING empno, ename BULK COLLECT INTO enums, names;
dbms_output.put_line('Deleted ' || SQL%ROWCOUNT || ' rows:');

FOR i IN enums.FIRST .. enums.LAST LOOP
dbms_output.put_line('Employee #' || enums(i) || ': ' || names(i));
END LOOP;

END;
/
DROP TABLE emp2;
```
上述:

- 计数方面有`.first`,`.last`,`.count`
- RETURNING 用来指定要返回的部分 ,BULK COLLECT INTO 成批聚合类型

## 批量操作


```sql
declare
  type empno_table_type is table of number(4) index by binary_integer;
  type ename_table_type is table of varchar2(20) index by binary_integer;

  v_empno_table empno_table_type;
  v_ename_table ename_table_type;
begin
    for i in 1..1000 loop
          v_empno_table(i):=i + 2000;
          v_ename_table(i):='name'||to_char(i);
    end loop;
  forall i in 1..v_empno_table.count
  --插入
  insert into my_emp values(v_empno_table(i),v_ename_table(i));
  --更新
  update my_emp set ename=v_ename_table(i) where empno=v_empno_table(i);
  --删除
  delete from my_emp where empno=v_empno_table(i);
end;
/
```

**sql%bulk_rowcount属性**

```sql
declare
  type deptno_table_type is table of number(2);
  v_deptno_table deptno_table_type:=deptno_table_type(10,20,30);
begin

  forall i in 1.. v_deptno_table.count
  update emp2 set sal=sal*0.5 where deptno=v_deptno_table(i);
  dbms_output.put_line('department_10:'||sql%bulk_rowcount(1)||' rows');
  dbms_output.put_line('department_10:'||sql%bulk_rowcount(1)||' rows');
  dbms_output.put_line('department_20:'||sql%bulk_rowcount(2)||' rows');
  dbms_output.put_line('department_30:'||sql%bulk_rowcount(3)||' rows');
end;
/
```
`SQL%BULK_ROWCOUNT(i)`表示 `FORALL` 语句第 i 元素所作用的行数

# 用户相关

```sql
create user scott identified by tiger;
alter user username identified by password;
grant connect,resource,create session,create table,unlimited tablespace to scott;
create tablespace spacename datafile '/Users/xx/data_oracle/xx.dbf' size 2M;
alter user scott default tablespace spacename;
alter user scott temporary tablespace temp;
```
