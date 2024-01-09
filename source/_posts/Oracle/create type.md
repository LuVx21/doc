---
title: Oracle自定义类型
date: 2017-04-10
tags:
- Oracle
---


<!-- TOC -->


<!-- /TOC -->


http://www.cnblogs.com/advocate/p/3729998.html

# 一: Oracle中的类型有很多种, 主要可以分为以下几类:

1. 字符串类型. 如: char, nchar, varchar2, nvarchar2.
2. 数值类型. 如: int, number(p,s), integer, smallint.
3. 日期类型. 如: date, interval, timestamp.
4. PL/SQL类型. 如: pls_integer, binary_integer, binary_double(10g), binary_float(10g), boolean. plsql类型是不能在sql环境中使用的, 比如建表时.
5. 自定义类型: type / create type.

# 二: type / create type 区别联系

相同:
可用用关键字create type 或者直接用type定义自定义类型,
区别:
`create type 变量 as table of 类型`

`create type 变量 as object(
字段1 类型1,
字段2 类型2
);`

与
` type 变量 is table of 类型`

`type 变量 is record(
字段1 类型1,
字段2 类型2
);`

区别是 用 create 后面用 as , 若直接用 type 后面用 is ,create 是创 object , 而 type 是创 record .
另 type用在语句块中,而create 是的独立的.


一般定义object的语法:

用
create type 自定义表类型A as table of 自定义Object类型A
和
create type 自定义Object类型A as object(
字段1 类型1,
字段2 类型2
);

与
type 自定义表类型B is table of 类型
和
type 自定义Object类型B is record(
字段1 类型1,
字段2 类型2
);


自定义类型一般分为两中,object类型和table类型.object类似于一个recored, 可以表示一个表的一行数据,

 object的字段就相当与表的字段.

自定义的table类型需要用的已经定义好的object类型.
# 三: type record用法概述
type 自定义Object类型B is record(
字段1 类型1,
字段2 类型2
);
## 3.1: 什么是记录(Record)?
由单行多列的标量构成的复合结构. 可以看做是一种用户自定义数据类型. 组成类似于多维数组.
将一个或多个标量封装成一个对象进行操作. 是一种临时复合对象类型.
记录可以直接赋值. RECORD1 :=RECORD2;
记录不可以整体比较.
记录不可以整体判断为空.
## 3.2: %ROWTYPE和记录(Record)?
请区别%ROWTYPE和记录(Record)类型. %ROWTYPE可以说是Record的升级简化版.
区别在与前者结构为表结构, 后者为自定义结构. 二者在使用上没有很大区别. 前者方便, 后者灵活. 在实际中根据情况来具体决定使用.
Record + PL/SQL表可以进行数据的多行多列存储.
## 3.3: 如何创建和使用记录?
  ①创建记录类型   语法:
```
TYPE  记录名  IS RECORD
　(
  filed1 type1 [NOT NULL] [: =eXPr1],
　     ....... ,
　  filedN typen [NOT NULL] [: =exprn]
 )
```

 其中, filed1是标量的名字.
  ②声明记录类型变量:     记录类型变量名 记录类型
  ③填充记录.
  ④访问记录成员    记录类型变量名.filed1    .........    记录类型变量名.filedN
  注意:    表字段类型修改后, 还需要修改记录字段类型, 有时候可能会忘记, 从而出现错误.    对于记录内每个字段(filed1.. . . ), 可以指定也可以使用%TYPE和%ROWTYPE动态指定记录字段类型.
  好处是表字段发生变化, 记录字段自动改变. 但是, 由于每次执行前, 遇到%TYPR或%ROWTYPE,   数据库系统都会去查看对应表字段类型, 会造成一定的数据库开销, 如果系统中大量使用记录类型, 则对性能会有一定影响.    另外如果删除了某一字段, 而自定义记录中使用了该字段, 也会有可能忘记删除该字段.         对数据库负荷偏低的系统, 性能问题一般可以不重点关注, 但是对于高负荷数据库服务器,        各个环节都要考虑性能问题, 每处节省一点出来, 性能整体就有很大提高.
  语法:

```
TYPE  记录名  IS RECORD
(
 filed1 table.Filed%Type [NOT NULL] [: =eXPr1] ,
 filed2 table.Filed%Type [NOT NULL] [: =eXPr1] ,
     ....... ,
  filedn table.Filed%Type [NOT NULL] [: =exprn]
);

```
例子: 记录可以整体赋值
```
/*conn scott/tiger
 Create Table  empa  As  Select * From emp;
 */
 Declare
     Type EmpType is Record(
        EMPNO number(4),
        ENAME  varchar2(10),
        JOB varchar2(15),
        SAL number(7,2),
        DEPTNO number(2)
     );
   EmpRec1  EmpType;
   EmpRec2  EmpType;
 Begin
   EmpRec1.Empno:=7369;
   EmpRec1.Ename:='SMITH';
   EmpRec1.Job:='CLERK';
   EmpRec1.Sal:=800;
   EmpRec1.Deptno:=10;
   EmpRec2 := EmpRec1;
   DBMS_output.put_line(EmpRec2.empno);
 End;
```
例子: 记录不可以整体比较, 只可以比较记录字段
```
Declare
     Type EmpType is Record(
        EMPNO number(4),
        ENAME  varchar2(10),
        JOB varchar2(15),
        SAL number(7,2),
        DEPTNO number(2)
     );
   EmpRec1  EmpType;
   EmpRec2  EmpType;
 Begin
   EmpRec1.Empno:=7369;
   EmpRec1.Ename:='SMITH';
   EmpRec1.Job:='CLERK';
   EmpRec1.Sal:=800;
   EmpRec1.Deptno:=10;
   if EmpRec1.sal < EmpRec2.sal then
     DBMS_output.put_line('Xiao Xiao Xiao');
   end if;
 End;
```
例子: 记录不可以整体判断为空, 只可以判断记录字段.
```
Declare
     Type EmpType is Record(
        EMPNO number(4),
        ENAME  varchar2(10),
        JOB varchar2(15),
        SAL number(7,2),
        DEPTNO number(2)
     );
   EmpRec  EmpType;
 Begin
   if EmpRec.ename is null then
     DBMS_output.put_line('Kong Kong Kong');
   end if;
 End;
```
例子: 使用%TYPE和%ROWTYPE动态指定记录字段.
```
/*conn scott/tiger
Create Table  empa  As  Select * From emp;
*/
DECLARE
 Type MyRecType Is  Record
 (
  RENO  EMPA.EMPNO%Type,
  RENAME   EMPA.ENAME%Type,
  RJOB   EMPA.JOB%Type
 );
 EmpRec  MyRecType;
Begin
 Select   EMPNO, ENAME, JOB  InTo  EmpRec  From empa Where empa.EMPNO = '7369';
 If  EmpRec.RJOB  = 'CLERK'  Then
  DBMS_OUTPUT.PUT_LINE('Name: '||EmpRec.RENAME);
 End If;
End;
```
例子: 数据集中的记录和记录类型中的数据关系.
```
DECLARE
  Type MyRecType Is  Record
  (
   RENO  EMPA.EMPNO%Type,
   RENAME   EMPA.ENAME%Type,
   RJOB   EMPA.JOB%Type
  );
  EmpRec  MyRecType;
  vJob  EMPA.JOB%Type;
 Begin
  Select   EMPNO, ENAME, JOB  InTo  EmpRec  From empa Where empa.EMPNO = '7369';
  DBMS_OUTPUT.PUT_LINE('MyRecType.RJOB: '||EmpRec.RJOB);
  EmpRec.RJOB  := '修改值后'  ;
  DBMS_OUTPUT.PUT_LINE('MyRecType.RJOB: '||EmpRec.RJOB);
  Select JOB  InTo vJob  from empa  Where empa.EMPNO = EmpRec.RENO;
  DBMS_OUTPUT.PUT_LINE('EMPA.JOB: '||vJob);
 End;
 /
```
3.4: 使用记录向表中插入数据?  根据表结构合理安排记录字段. 比如主外键.  如果用记录(RECORD)插入数据, 那么只能使用记录成员;  如果用%ROWTYPE插入数据,可以直接使用%ROWTYPE.
例子: 使用记录成员向表中插入数据
```
DECLARE
 Type MyRecType Is  Record
 (
  RENO  EMPA.EMPNO%Type,
  RENAME   VARCHAR2(10),
  RJOB   EMPA.JOB%Type
 );
 EmpRec MyRecType;
Begin
 Select   EMPNO, ENAME, JOB  InTo  EmpRec  From empa Where empa.EMPNO = '7369';
 DBMS_OUTPUT.PUT_LINE(EmpRec.RENO||'  '||EmpRec.RENAME||'  '||EmpRec.RJOB);
 EmpRec.RENO  := 1001;
 EmpRec.RENAME := '杰克';
 EmpRec.RJOB  := '办事员';
 Insert InTo  empa(EMPNO,ENAME,JOB)  Values(EmpRec.RENO, EmpRec.RENAME,EmpRec.RJOB);
 Select  EMPNO, ENAME, JOB  InTo  EmpRec  From empa Where empa.EMPNO = '1001';
 DBMS_OUTPUT.PUT_LINE(EmpRec.RENO||'  '||EmpRec.RENAME||'  '||EmpRec.RJOB);
End;
```
3.5: 使用记录更新数据?  如果用记录(RECORD)更新数据, 那么只能使用记录成员;  如果用%ROWTYPE更新数据,可以直接使用%ROWTYPE.
例子: 使用%ROWTYPE向表中插入数据
```
DECLARE
 vEmp empa%RowType;
Begin
 Select  *  InTo  vEmp  From empa Where empa.EMPNO = '7369';
 UpDate empa Set ROW = vEmp Where EMPNO = 1001;
End;
```
3.6: 使用记录删除数据?  删除记录时, 只能在delete语句的where子句中使用记录成员.

# 四: type  table用法
4.1: 定义
type 变量 is table of 类型

```
TYPE orders_type IS TABLE OF all_orders%ROWTYPE;
```

4.2:用法

1. TYPE tabletype IS TABLE  OF type INDEX BY BINARY_INTEGER;
定义:TYPE t_charTable IS TABLE OF VARCHAR2(10) INDEX BY BINARY_INTEGER;
引用:tableName(index);
例子:

```
declare
   type t_table is table of varchar2(10) indexby BINARY_integer;
   MyTab   t_table;
begin
   MyTab(1) :=  'A';
   MyTab(2) :=  'B';
   MyTab(3) :=  'C';
   DBMS_OUTPUT.PUT_LINE('First index:'||'   '|| mytab(1) ||'   ');
end;
```
```
DECLARE
     TYPE t_StudentTable IS TABLE OF students%ROWTYPE INDEXBY BINARY_INTEGER;
     v_Students t_StudentTable;
BEGIN
SELECT * INTO v_Students(1100)
FROM students
WHERE id=1100;
     DBMS_OUTPUT.PUT_LINE( v_Students(1100).OUUSRNM);
END;
```



--record table综合使用


```
/*conn scott/tiger
 Create table empa as select * from emp;
*/
--例子:
Declare
 Type RecType Is Record
 (
  rno  empa.empno%type,
  rname empa.ename%type,
  rsal  empa.sal%type
 );
 Type TabType Is Table Of  RecType  Index By Binary_Integer;
 MyTab TabType;
 vN Number;
Begin
 --填充
 vN := 1;
 For varR In (Select  *  From empa Order By empno ASC)
 Loop
  MyTab(vN).rno  := varR.empno;
  MyTab(vN).rname := varR.ename;
  MyTab(vN).rsal := varR.sal;
  vN := vN + 1;
 End Loop;
 --访问
 vN := MyTab.First;
 For varR In vN..MyTab.count
 Loop
  DBMS_OUTPUT.PUT_LINE(vN ||'   '||MyTab(vN).rno||'   '||MyTab(vN).rname||'   '||MyTab(vN).rsal);
  vN := MyTab.Next(vN);
 End Loop;
End;
```


注意:
Oracle中index by binary_integer的作用
如语句: type  numbers  is table of number index by binary_integer;其作用是,加了"index by binary_integer "后, numbers类型的下标就是自增长, numbers类型在插入元素时, 不需要初始化, 不需要每次extend增加一个空间.
　　而如果没有这句话"index by binary_integer", 那就得要显示对初始化, 且每插入一个元素到numbers类型的table中时, 都需要先extend.
　　示例:
　　没加"index by binary_integer"时:

```
declare
type numbers is table of number;
n    numbers := numbers();
begin
n.extend;
n(1) := 2;
n.extend;
n(2) := 3;
for i in1 .. n.count loop
dbms_output.put_line(n(i));
end loop;
end;
```

--输出: 2, 3
　　而如果加了"index by binary_integer", 代码如下写就可以达到上面的效果


```
declare
　　type numbers is table of number index by binary_integer;
　　n numbers;
　　begin
　　n(1) := 2;
　　n(2) := 3;
　　for i in1 .. n.count loop
　　dbms_output.put_line(n(i));
　　end loop;
　　end;
```



# 五: create type 用法
5.1:定义
概念

  方法:是在对象类型说明中用关键字  MEMBER   声明的子程序

  方法是作为对象类型定义组成部分的一个过程或函数

  方法不能与对象类型或其任何属性同名

  与数据包程序相似, 大多数方法有两个部分




CREATE   [OR   REPLACE]   TYPE   <typename>   AS   OBJECT
(attribute1   datatype,
    :
 attributeN   datatype
 MEMBER   PROCEDURE   <methodname>   (parameter,  mode,  datatype),
 MEMBER   FUNCTION   <methodname>   (parameter,  mode,  datatype)
 RETURN   datatype,PRAGMA   RESTRICT_REFERENCES
 (<methodname>,WNDS/RNDS/WNPS/RNPS)
);
  说明: PRAGMA   RESTRICT_REFERENCES指定MEMBER方法按以下模式之一   操作:

   –WNDS   (不能写入数据库状态)   不能修改数据库

  –RNDS   (不能读出数据库状态)   不能执行查询

  –WNPS   (不能写入数据包状态)   不能更改数据包变量的值

  –RNPS   (不能读出数据包状态)   不能引用数据包变量的值

  例:


create   or   replace   type   FLIGHT_SCH_TYPE   as   object
      (FLIGHTNO   VARCHAR2(4)   ,  AIRBUSNO   VARCHAR2(5)   ,
       ROUTE_CODE   VARCHAR2(7)   ,  DEPRT_TIME   VARCHAR2(10)   ,
       JOURNEY_HURS   VARCHAR2(10)   ,  FLIGHT_DAY1   NUMBER(1)   ,
       FLIGHT_DAY2   NUMBER(1)   ,
       Member   function   DAYS_FN(FLIGHT_DAY1   in   number)   return   varchar2   ,
       Pragma       restrict_references(DAYS_FN   ,  WNDS))   ;

创建对象类型方法主体


CREATE   [OR   REPLACE]   TYPE   BODY   <typename>   AS MEMBER
   FUNCTION   <methodname>   (parameter   dataype)   RETURN
  <datatype>   IS<PL/SQL_block>;MEMBER   PROCEDURE   <methodname>
(parameter   datatype);
END;
  例:


create   or   replace   type   body   FLIGHT_SCH_TYPE   as
       member   function   DAYS_FN(FLIGHT_DAY1   in   number)   return   varchar2
       is
      disp_day   varchar2(20)   ;
      begin
      if   flight_day1   =   1   then
           disp_day   :=   'Sunday'   ;
     elsif   flight_day1   =   2   then
           disp_day   :=   'Monday'   ;
     elsif   flight_day1   =   3   then
          disp_day   :=   'Tuesday'   ;
     elsif   flight_day1   =   4   then
         disp_day   :=   'Wednesday'   ;
     elsif   flight_day1   =   5   then
         disp_day   :=   'Thursday'   ;
    elsif   flight_day1   =   6   then
         disp_day   :=   'Friday   '   ;
       elsif   flight_day1   =   7   then
         disp_day   :=   'Saturday'   ;
    end   if   ;
     return   disp_day   ;
  end   ;
end   ;
调用对象方法基于类型创建表后, 就可以在查询中调用对象方法



  A． 创建基于对象的表语法:

create   table   <表名>   of   <对象类型>意义: 此表具有该类型和member方法的所有属性, 

我们不能通过DBA   STUDIO的表数据编辑器来编辑数据.

例: create   table   FLIGHT_SCH_TAB   of   FLIGHT_SCH_TYPE

    insert   into   FLIGHT_SCH_TAB     values('SL36','AB02','SAN-LOU','5','13:30',3,6);



  B.访问对象表中的MEMBER方法

SELECT   <columnname>,  <aliasname>.<methodname(parameters)>FROM   <tablename>   <aliasname>;

例: select   flightno,route_code,f.days_fn(flight_day1) as  FLIGHTDAY from FLIGHT_SCH_TAB  f;



  C．关系表中的字段为对象类型

create   table   FLIGHT_SCH_TABS(FLIGHT_DET   FLIGHT_SCH_TYPE   ,    FLIGHT_DESC   varchar2(20))   ;

注: 插入数据, 对于对象类型的字段的值, 需要通过构造函数来得到. 对象类型名称(成员1,..成员n)

例: insert   into   FLIGHT_SCH_TABS     values(FLIGHT_SCH_TYPE('SL36','AB02','SAN-LOU','5','13:30',3,6),'DESC1');



  D．访问关系表中的Member方法此处的关系表: 指表中有字段为对象类型

SELECT   <columnname>,  <aliasname>.<columnname>.<methodname (parameters)>FROM   <tablename>   <aliasname>;

例: select f.flight_det.FLIGHTNO,f.flight_det.ROUTE_CODE,f.flight_det.DAYS_FN(f.flight_det.FLIGHT_DAY1) FLIGHTDAY from flight_sch_tabs f;

1． 声明简单类型内容包括: A   对象类型的创建B.基于对象的表的创建插入与访问C   关系对象表的创建插入与访问



  2． 通过value运算符访问基于类型的表

select   value(<aliasname>     From   <objecttable>   <aliasname>

例: select   value(A)   FROM   FLIGHT_SCH_TAB



  A   --返回的是对象区别

select   *   from   FLIGHT_SCH_TAB;--返回的是单个的值



  3． REF   运算符使您可以引用对象表中现有行的   OID   值   REF   运算符将表别名作为输入, 并且为行对象返回   OID

    语法: select   REF(<aliasname>)   from   <objecttable>   <aliasname>

例: select   ref(a)   from   FLIGHT_SCH_TAB   a   ;注: FLIGHT_SCH_TAB   是基于对象的表



  4．声明复合类型

CREATE   TYPE   name_type AS   OBJECT(name   VARCHAR2(20),  address   address_type);

  5．定义对象之间的关系也是通过关键字REF,前面我们能过REF查询了基于对象的表中的对象的OID值, 这里我们讲REF的另一个用途, 即通过REF来定义对象之间的关系

  称为引用的   REF   允许您创建行对象指针

  它将创建对被引用对象位置的引用i该指针用于查询, 更新或删除对象iREF   由目标对象的   OID, 数据库标识符(列)和对象表构成iOID   用于创建使用   REF   和   DEREF   运算符的外键列的关系

  iSQL   和   PL/SQL   语句必须使用   REF   函数来处理对象引用

  可按如下步骤关联两个表



  1. 创建对象类型, 下面我们会创建另一个表, 这个表的一个字段的类型为此类型

create   or   replace   type   type_class   as   object(     classid   varchar2(10)   ,    classname   varchar2(10))/



  2. 创建基于此类型的表create     table   tbl_type_class   of   type_class



  3. 创建具有外键列的关系表, 有一个外键将引用1中定义的类型, 并且该外键的值在2中已有的数据已经存在create   table   tbl_student_ref(     stuid   varchar2(20)   ,    stuname   varchar2(20)   ,    age   number(10)   ,    grade   ref   type_class   scope   is   tbl_type_class)



  4． 将数据插入到对象表中begin     insert   into   tbl_type_class   values('gid1'   ,  'gname1')   ;

       insert   into   tbl_type_class   values('gid2'   ,  'gname2')   ;     commit   ;end   ;/



  5．将数据插入到关系对象表中, 必须从上面创建的对象表中引用数据;

insert   into   tbl_student_ref     select       'stuid1',  'stuname1',20,ref(a)         from   tbl_type_class   a         where   classid='gid1'

注: 下面的方法是不行的！insert   into   tbl_student_ref   values(     'stuid2',    'stuname2',    20,    select   ref(a)   from   tbl_type_class   a   where   classid='gid1'   )/



  6．服从值若要查看引用的值, 则需要使用DEREF运算符i语法SELECT   DEREF(<列名>.<列名>)FROM   <表名>   <别名>;例: select   deref(grade)   from   tbl_student_ref





简单用法:

```sql
create or replace type typ_calendar as object(
    年 varchar2(8),
    月 varchar2(8),
    星期日 varchar2(8),
    星期一 varchar2(8),
    星期二 varchar2(8),
    星期三 varchar2(8),
    星期四 varchar2(8),
    星期五 varchar2(8),
    星期六 varchar2(8),
    本月最后一日 varchar2(2)
);
--这种类型可以在表结构定义的时候使用:
create table tcalendar of typ_calendar;
--插入数据测试:
SQL> insert into tcalendar
  2  select typ_calendar('2010','05','1','2','3','4','5','6','7','31') from dual
  3  /
--注意: 插入的数据需要用typ_calendar进行转换.
1 row inserted
--查看结果
SQL> select * from tcalendar;
年       月       星期日   星期一   星期二   星期三   星期四   星期五   星期六   本月最后一日
-------- -------- -------- -------- -------- -------- -------- -------- -------- ------------
2010     05       1        2        3        4        5        6        7        31
```

# 复杂用法:

一, 抽象数据类型

1, 创建类型

--地址类型

CREATE OR REPLACE TYPE AddressType AS OBJECT

(

Country varchar2(15),

City varchar2(20),

Street varchar2(30)

);



2, 类型嵌套

--创建基于前一个类型的新的抽象数据类型: 巨星类型

1
2
3
4
5
CREATE OR REPLACE TYPE SuperStarType AS OBJECT
(
StarName varchar2(30),
Address AddressType
);


3, 基于抽象类型创建关系表

1
2
3
4
5
CREATE TABLE SuperStar
(
StarID varchar(10),
Star SuperStarType
);


4, 基于抽象类型创建对象表

1
CREATE TABLE SuperStarObj of SuperStarType;


5, 使用构造方法在表中插入记录

1
INSERT INTO SuperStar VALUES(''001'',SuperStarType(''Zidane'',AddressType(''France'',''Paris'',''People Street NO.1'')));


6, 查询表中记录

1
2
3
4
5
6
7
8
9
10
11
12
(1)SQL> SELECT * FROM SuperStar;
STARID
----------
STAR(STARNAME, ADDRESS(COUNTRY, CITY, STREET))
--------------------------------------------------------------------------------
001
SUPERSTARTYPE(''Zidane'', ADDRESSTYPE(''France'', ''Paris'', ''People Street NO.1''))
(2)
SELECT s.StarID,s.Star.StarName,s.Star.Address.Country,s.Star.Address.City,s.Star.Address.Street FROM SuperStar s
STARID STAR.STARNAME STAR.ADDRESS.CO STAR.ADDRESS.CITY STAR.ADDRESS.STREET
---------- ------------------------------ --------------- -------------------- ---------------------
001 Zidane France Paris People Street NO.1


7, 抽象数据类型的继承

(1)创建一个类型

1
2
3
4
5
6
CREATE OR REPLACE TYPE PersonType AS OBJECT
(
PersonName varchar(10),
PersonSex varchar(2),
PersonBirth date
) not final;
(2)派生一个类型

1
2
3
4
5
CREATE OR REPLACE TYPE StudentType UNDER PersonType
(
StudentNO int,
StudentScore int
);
(3)查看数据字典

1
2
3
4
5
6
7
8
9
SQL> DESC StudentType
StudentType extends SYS.PERSONTYPE
Name
------------------------------------------------------------------------------
PERSONNAME
PERSONSEX
PERSONBIRTH
STUDENTNO
STUDENTSCORE
(4)创建对象表

1
CREATE TABLE student OF StudentType;
(5)向对象表中插入数据

1
INSERT INTO student VALUES(''Rose'',''nv'',to_date(''1983-05-02'',''yyyy-mm-dd''),1001,98);
(6) 查询数据

1
2
3
4
SQL> SELECT * FROM student;
PERSONNAME PE PERSONBIR STUDENTNO STUDENTSCORE
---------- -- --------- ---------- ------------
Rose nv 02-MAY-83 1001 98


二, 可变数组

1, 创建带有可变数组的表

(1)创建可变数组的基类型

1
2
3
4
5
6
CREATE OR REPLACE TYPE MingXiType AS OBJECT
(
GoodID varchar2(20),
InCount int,
ProviderID varchar(20)
);
(2)创建嵌套项类型的可变数组

1
CREATE OR REPLACE TYPE arrMingXiType AS VARRAY(100) OF MingXiType;
(3)创建一个主表

1
2
3
4
5
6
7
CREATE TABLE InStockOrder
(
OrderID varchar(15) Not Null Primary Key,
InDate date,
OperatorID varchar(15),
MingXi arrMingXiType
);


2, 操作可变数组

(1)插入数据

1
2
3
4
5
INSERT INTO InStockOrder
VALUES(''200710110001'',TO_DATE(''2007-10-11'',''YYYY-MM-DD''),''007'',
arrMingXiType(MingXiType(''G001'',100,''1001''),
MingXiType(''G002'',888,''1002''))
);
(2)查询数据

1
2
3
4
5
6
7
SQL> SELECT * FROM InStockOrder;
ORDERID INDATE OPERATORID
--------------- --------- ---------------
MINGXI(GOODID, INCOUNT, PROVIDERID)
----------------------------------------------------------------------
200710110001 11-OCT-07 007
ARRMINGXITYPE(MINGXITYPE(''G001'', 100, ''1001''), MINGXITYPE(''G002'', 888, ''1002'')
(3)使用Table()函数

1
2
3
4
5
6
SQL> SELECT * FROM Table(SELECT t.MingXi FROM InStockOrder t
WHERE t.OrderID=''200710110001'');
GOODID INCOUNT PROVIDERID
-------------------- ---------- --------------------
G001 100 1001
G002 888 1002
(4)修改数据

1
2
3
4
UPDATE InStockOrder
SET MingXi=arrMingXiType(MingXiType(''G001'',200,''1001''),
MingXiType(''G002'',8888,''1002''))
WHERE OrderID=''200710110001''
注意: 不能更新VARRAY中的单个元素, 必须更新整个VARRAY



三, 嵌套表

1, 创建嵌套表

(1)创建嵌套表的基类型

1
2
3
4
5
6
CREATE OR REPLACE TYPE MingXiType AS OBJECT
(
GoodID varchar2(20),
InCount int,
ProviderID varchar(20)
) not final;
(2)创建嵌套表类型

1
CREATE OR REPLACE TYPE nestMingXiType AS TABLE OF MingXiType;
(3)创建主表, 其中一列是嵌套表类型

1
2
3
4
5
6
7
CREATE TABLE InStockTable
(
OrderID varchar(15) Not Null Primary Key,
InDate date,
OperatorID varchar(15),
MingXi nestMingXiType
) Nested Table MingXi STORE AS MingXiTable;


2, 操作嵌套表

(1)向嵌套表中插入记录

1
2
3
4
5
6
INSERT INTO InStockTable
VALUES(''20071012001'',TO_DATE(''2007-10-12'',''YYYY-MM-DD''),''007'',
nestMingXiType(MingXiType(''G003'',666,''1001''),
MingXiType(''G004'',888,''1002''),
MingXiType(''G005'',8888,''1003''))
);
(2)查询数据

1
2
3
4
5
6
7
SQL> SELECT * FROM InStockTable;
ORDERID INDATE OPERATORID
--------------- --------- ---------------
MINGXI(GOODID, INCOUNT, PROVIDERID)
----------------------------------------------------------------------------------------------------
20071012001 12-OCT-07 007
NESTMINGXITYPE(MINGXITYPE(''G003'', 666, ''1001''), MINGXITYPE(''G004'', 888, ''1002''), MINGXITYPE(''G005'', 8888, ''1003'')
(3)使用Table()函数

1
2
3
4
5
6
7
SQL> SELECT * FROM Table(SELECT T.MingXi FROM InStockTable t
WHERE OrderID=''20071012001'')
GOODID INCOUNT PROVIDERID
-------------------- ---------- --------------------
G003 666 1001
G004 888 1002
G005 8888 1003
(4)更新嵌套表中的数据

1
2
UPDATE Table(SELECT t.MingXi FROM InStockTable t WHERE OrderID=''20071012001'') tt
SET tt.InCount=1666 WHERE tt.GoodID=''G003'';
(5)删除表中数据

1
2
DELETE Table(SELECT t.MingXi FROM InStockTable t WHERE OrderID=''20071012001'') tt
WHERE tt.GoodID=''G003''


四, 对象表

1, 创建对象表

1
CREATE TABLE ObjectTable OF MingXiType;
2, 向表中插入数据

1
2
INSERT INTO ObjectTable VALUES(''G001'',500,''P005'');
INSERT INTO ObjectTable VALUES(''G002'',1000,''P008'');
3, 查询对象表中的记录

1
2
3
4
5
6
7
8
9
10
11
12
--A 直接查询
SQL> SELECT * FROM ObjectTable;
GOODID INCOUNT PROVIDERID
-------------------- ---------- ---------------
G001 500 P005
G002 1000 P008
--B 用VALUE()函数查询
SQL> SELECT VALUE(O) FROM ObjectTable O;
VALUE(O)(GOODID, INCOUNT, PROVIDERID)
------------------------------------------
MINGXITYPE(''G001'', 500, ''P005'')
MINGXITYPE(''G002'', 1000, ''P008'')
4, 查看对象标志符(OID)

--A REF操作符引用行对象
SQL> SELECT REF(t) FROM ObjectTable t;
REF(T)
--------------------------------------------------------------------------------
0000280209771F103ED34842478A9C439CDAEFEF6324B0ACF849F14BD7A8B52F4B0297D1C90040A9
5A0000
0000280209A2D3359E0F0C44B3AF652B944F8823F524B0ACF849F14BD7A8B52F4B0297D1C90040A9
5A0001
--B 将OID用于创建外键
CREATE TABLE Customer
( CustomerID varchar(10) PRIMARY KEY,
CustomerName varchar(20),
CustomerGoods REF MingXiType SCOPE IS ObjectTable,--引用MingXiType外键, 关联的是OID的值
CustomerAddress varchar(20)
);
--C 向Customer表中插入数据, 此表将从上面创建的对象表中引用数据
INSERT INTO Customer SELECT ''007'',''Yuanhy'',REF(O),''France''
FROM ObjectTable O
WHERE GoodID=''G001'';
--D 查询Customer表
SQL> SELECT * FROM Customer;
CUSTOMERID CUSTOMERNAME
---------- --------------------
CUSTOMERGOODS
-----------------------------------------------------------------------------
CUSTOMERADDRESS
--------------------
007 Yuanhy
0000220208771F103ED34842478A9C439CDAEFEF6324B0ACF849F14BD7A8B52F4B0297D1C9
France
--E 用DEREF操作符返回对象的值
SQL> SELECT CustomerID,CustomerName,DEREF(t.CustomerGoods),CustomerAddress
2 FROM Customer t;
CUSTOMERID CUSTOMERNAME
---------- --------------------
DEREF(T.CUSTOMERGOODS)(GOODID, INCOUNT, PROVIDERID)
----------------------------------------------------------------------------
CUSTOMERADDRESS
--------------------
007 Yuanhy
MINGXITYPE(''G001'', 500, ''P005'')
France


五, 对象视图

将关系表化装成对象表

1, 创建对象视图

A 创建基于关系表父表的对象类型

CREATE OR REPLACE TYPE depttype AS OBJECT
(
deptid number(10),
deptname varchar(30),
loc number(10)
);
B 创建基于关系表的对象视图


CREATE VIEW deptview OF depttype WITH OBJECT OID(deptid) AS
SELECT department_id,department_name,location_id FROM dept;
C 查询视图

SQL> SELECT * FROM deptview;
DEPTID DEPTNAME LOC
---------- ------------------------------ ----------
10 Administration 1700
20 Marketing 1800
30 Purchasing 1700
40 Human Resources 2400
50 Shipping 1500
60 IT 1400
70 Public Relations 2700
SQL> select ref(t) from deptview t;
REF(T)
----------------------------------------------------------------------------------------------------
00004A038A004667BAC3685B444520A60ED30027E8F25F0000001426010001000100290000000000090604002A00078401FE
00004A038A004667BAC3685B444520A60ED30027E8F25F0000001426010001000100290000000000090604002A00078401FE
00004A038A004667BAC3685B444520A60ED30027E8F25F0000001426010001000100290000000000090604002A00078401FE
00004A038A004667BAC3685B444520A60ED30027E8F25F0000001426010001000100290000000000090604002A00078401FE
00004A038A004667BAC3685B444520A60ED30027E8F25F0000001426010001000100290000000000090604002A00078401FE
00004A038A004667BAC3685B444520A60ED30027E8F25F0000001426010001000100290000000000090604002A00078401FE
00004A038A004667BAC3685B444520A60ED30027E8F25F0000001426010001000100290000000000090604002A00078401FE
2, 创建引用视图(类似于关系表创建一个从表)

1
2
CREATE VIEW empview AS SELECT MAKE_REF(deptview,department_id) deptOID,employee_id,
first_name,last_name FROM emp;


查询对象视图empview

SQL> SELECT * FROM empview;
DEPTOID
----------------------------------------------------------------------------------------------------
EMPLOYEE_ID FIRST_NAME LAST_NAME
----------- -------------------- -------------------------
00004A038A004667BAC3685B444520A60ED30027E8F25F0000001426010001000100290000000000090604002A00078401FE
100 Steven King
00004A038A004667BAC3685B444520A60ED30027E8F25F0000001426010001000100290000000000090604002A00078401FE
101 Neena Kochhar
00004A038A004667BAC3685B444520A60ED30027E8F25F0000001426010001000100290000000000090604002A00078401FE
102 Lex De Haan
00004A038A004667BAC3685B444520A60ED30027E8F25F0000001426010001000100290000000000090604002A00078401FE
103 Alexander Hunold
Oracle对象类型也有属性和方法．

　　创建对象类型与创建表很相似, 只是实际上不为存储的数据分配空间:

　　不带方法的简单对象类型:



CREATE TYPE type_name as OBJECT (

column_1 type1,

column_2 type2,

...

);

　　注意: AS OBJECT

　　创建好对象类型之后, 就可以在创建表的时候, 使用该类型了, 如:



CREATE TYPE HUMAN AS OBJECT(

NAME VARCHAR2(20),

SEX VARCHAR2(1),-- F : FEMALE M:MALE

BIRTHDAY DATE,

NOTE VARCHAR2(300)

)

　　稍后, 可以用下面的语句查看:



SELECT * FROM USER_OBJECTS WHERE OBJECT_TYPE = ''TYPE''

CREATE TABLE STUDENTS(

GUID NUMBER NOT NULL,

STUDENTS HUMAN

)

　　此下省去两个Trigger.

　　插入数据的时候, 可以如下:



INSERT INTO STUDENTS (STUDENT) VALUES (HUMAN(''xling'',''M'',TO_DATE(''20060101'',''YYYYMMDD''),''测试''))

　　注意: HUMAN(''xling'',''M'',TO_DATE(''20060101'',''YYYYMMDD''),''测试''), 这是个默认的构造函数．

　　如果想选出性别为女(F)的记录, 可以如下:



SELECT * FROM STUDENTS S WHERE S.STUDENT.SEX = ''F''

　　注意: 不能写成: SELECT * FROM STUDENTS WHERE STUDENT.SEX = ''F'' 这样会报如下错误: ORA-00904: "STUDENT"."SEX": 标识符无效



　　对象类型表: 每条记录都是对象的表, 称为对象类型表．它有两个使用方法: 1, 用作只有一个对象类型字段的表．2,用作具有对象类型字段的标准关系表．

　　语法如下:



CREATE TABLE table_name OF object_type;

　　例如:



CREATE TABLE TMP_STUDENTS OF HUMAN;

　　用DESC TMP_STUDENTS, 可以看到它的字段结构和HUMAN的结构一样．

　　对象类型表有两个优点: 1,从某种程度上简化了对象的使用, 因为对象表的字段类型与对象类型是一致的, 所以, 不需要用对象名来修饰对象属性, 可以把数据插入对象类型表, 就像插入普通的关系表中一样:

INSERT INTO TMP_STUDENTS VALUES (''xling'',''M'',TO_DATE(''20060601'',''YYYYMMDD''),''对象类型表'');

　　当然也可用如下方法插入:

INSERT INTO TMP_STUDENTS VALUES (HUMAN(''snow'',''F'',TO_DATE(''20060102'',''YYYYMMDD''),''用类型的构造函数''));

　　第二个特点是: 对象表是使用对象类型作为模板来创建表的一种便捷方式, 它可以确保多个表具有相同的结构．

　　对象类型表在: USER_TABLES表里是查不到的, 而在USER_OBJECTS表里可以查到, 而且OBJECT_TYPE = ''TABLE''

　　类型在定义的时候, 会自动包含一个方法, 即默认的构造函数．构造函数的名称与对象的名称相同, 它带有变量与对象类型的每个属性相对应．



　　对象类型的方法:



CREATE TYPE type_name AS OBJECT (

column1 column_type1,

column2 column_type2,

... ,

MEMBER FUNCTION method_name(args_list) RETURN return_type,

...

)

　　注意: 是MEMBER FUNCTION,(当然, 也可是MEMBER PROCEDURE,没有返回值)

　　和包(PACKAGE)一样, 如果对象类型有方法的话, 还要声明一个BODY:


CREATE TYPE BODY type_name AS
MEMBER FUNCTION method_name RETURN return_type {AS | IS}
variable declareations..
BEGIN
CODE..
RETURN return_value;
END;//END MEMBER FUNCTION
...
END;//END TYPE BODY
　--　如下所示:
--
CREATE TYPE HUMAN AS OBJECT(
NAME VARCHAR2(20),
SEX VARCHAR2(1),-- F : FEMALE M:MALE
BIRTHDAY DATE,
NOTE VARCHAR2(300),
MEMBER FUNCTION GET_AGE RETURN NUMBER
)
--BODY
CREATE TYPE BODY HUMAN AS
MEMBER FUNCTION GET_AGE RETURN NUMBER AS
V_MONTHS NUMBER;
BEGIN
SELECT FLOOR(MONTHS_BETWEEN(SYSDATE,BIRTHDAY)/12) INTO V_MONTHS FROM DUAL;
RETURN V_MONTHS;
END;
END;




　　注意: BODY的格式, 不是AS OBJECT,也不是用小括号括起来的．MEMBER FUNCTION 后的AS或IS不能省略．

　　还以STUDENTS表为例(注: 如果类型以被某个表使用, 是不能修改的, 必须把相关的表删除, 然后把类型删除, 在一个一个新建, 这里就省略了, 参见前文所述)

SELECT S.STUDENT.GET_AGE() FROM STUDENTS S



　　在提起注意: 表名一定要有别名．GET_AGE()的括号不能省略, 否则会提示错误．

　　下面演示在一个匿名过程中的使用情况:

SET SERVEROUTPUT ON

DECLARE

AA HUMAN;

AGE NUMBER;

BEGIN

AA := HUMAN(''xingFairy'',''M'',TO_DATE(''19830714'',''YYYYMMDD''),''过程'');

AGE := AA.GET_AGE();

DBMS_OUTPUT.PUT_LINE(AGE);

END;

　　映射方法:

　　映射方法是一种不带参数, 并返回标准的标量Oracle SQL数据类型的方法, 如NUMBER,VARCHAR2, Oracle将间接地使用这些方法执行比较运算．

　　映射方法最重要的一个特点是: 当在WHERE或ORDER BY等比较关系子句中使用对象时, 会间接地使用映射方法．

　　映射方法的声明只过是在普通方法声明的前面加一个 MAP而以, 注意: 映射方法是一种不带参数的方法．

MAP MEMBER FUNCTION function_name RETURN return_type

　　修改前文提到的HUMAN类型:

--映射方法　MAP

CREATE TYPE HUMAN AS OBJECT(
NAME VARCHAR2(20),
SEX VARCHAR2(1),-- F : FEMALE M:MALE
BIRTHDAY DATE,--注册日期　
REGISTERDAY DATE,
NOTE VARCHAR2(300),
MEMBER FUNCTION GET_AGE RETURN NUMBER,
MAP MEMBER FUNCTION GET_GRADE RETURN NUMBER
)
CREATE TYPE BODY HUMAN AS
-----------------------
MEMBER FUNCTION GET_AGE RETURN NUMBER AS
V_MONTHS NUMBER;
BEGIN
SELECT FLOOR(MONTHS_BETWEEN(SYSDATE,BIRTHDAY)/12) INTO V_MONTHS FROM DUAL;
RETURN V_MONTHS;
END;
------------------------
MAP MEMBER FUNCTION GET_GRADE RETURN NUMBER AS
BEGIN
RETURN MONTHS_BETWEEN(SYSDATE,BIRTHDAY);
END;
END;
　　插入数据:


INSERT INTO STUDENTS (STUDENT) VALUES (HUMAN(''xling'',''M'',TO_DATE(''19830714'',''YYYYMMDD''),TO_DATE(''20020915'',''YYYYMMDD''),'' 测试MAP方法''));
INSERT INTO STUDENTS (STUDENT) VALUES (HUMAN(''fairy'',''M'',TO_DATE(''19830714'',''YYYYMMDD''),TO_DATE(''20010915'',''YYYYMMDD''),'' 测试MAP方法''));
INSERT INTO STUDENTS (STUDENT) VALUES (HUMAN(''snow'',''M'',TO_DATE(''19830714'',''YYYYMMDD''),TO_DATE(''20020915'',''YYYYMMDD''),'' 测试MAP方法''));
　　在执行上面的操作后, 用下面这个SELECT语句可以看出映射方法的效果:

SELECT S.STUDENT.NAME,S.STUDENT.GET_GRADE() FROM STUDENTS S ORDER BY STUDENT

　　它是按MAP方法GET_GRADE()的值进行排序的．注意是ORDER BY STUDENT,在提起一次需要注意, 一定要用表的别名, 方法后的括号不能省略, 即使没有参数．

　　如果想以MAP方法的结果为条件, 可以如下:


SELECT S.STUDENT.NAME,S.STUDENT.GET_GRADE() FROM STUDENTS S WHERE S.STUDENT.GET_GRADE() > 50
SELECT S.STUDENT.NAME,S.STUDENT.GET_GRADE() FROM STUDENTS S WHERE STUDENT > HUMAN(NULL,NULL,NULL,TO_DATE(''20020101'',''YYYYMMDD''),NULL);
　　排序方法:

　　先说一下SELF, Oracle里对象的SELF和JAVA里的this是同一个意思．

　　对象的排序方法具有一个与对象类型相同的参数, 暂称为ARG1, 用于和SELF对象进行比较．如果调用方法的SELF对象比ARG1小, 返回负值, 如果相等, 返回0, 如果SELF大于ARG1, 则返回值大于0．

--映射方法　MAP


CREATE TYPE HUMAN AS OBJECT(
NAME VARCHAR2(20),
SEX VARCHAR2(1),-- F : FEMALE M:MALE
BIRTHDAY DATE,
REGISTERDAY DATE,
NOTE VARCHAR2(300),
MEMBER FUNCTION GET_AGE RETURN NUMBER,
ORDER MEMBER FUNCTION MATCH(I_STUDENT IN HUMAN) RETURN NUMBER
)
CREATE TYPE BODY HUMAN AS
-----------------------
MEMBER FUNCTION GET_AGE RETURN NUMBER AS
V_MONTHS NUMBER;
BEGIN
SELECT FLOOR(MONTHS_BETWEEN(SYSDATE,BIRTHDAY)/12) INTO V_MONTHS FROM DUAL;
RETURN V_MONTHS;
END;
------------------------
ORDER MEMBER FUNCTION MATCH(I_STUDENT IN HUMAN) RETURN NUMBER AS
BEGIN
RETURN REGISTERDAY - I_STUDENT.REGISTERDAY;
END;
END;
　　注意: 在声明的时候, ORDER方法的参数类型要和SELF的类型一致．

SET SERVEROUTPUT ON

DECLARE

S1 HUMAN;

S2 HUMAN;

BEGIN



S1 := HUMAN(''xling'',NULL,NULL,TO_DATE(''20020915'',''YYYYMMDD''),NULL);

S2 := HUMAN(''snow'',NULL,NULL,TO_DATE(''20010915'',''YYYYMMDD''),NULL);



IF S1 > S2 THEN

DBMS_OUTPUT.PUT_LINE(S1.NAME);

ELSIF S1 < S2 THEN

DBMS_OUTPUT.PUT_LINE(S2.NAME);

ELSE

DBMS_OUTPUT.PUT_LINE(''EQUAL'');

END IF;

END;

　　注意S1 和 S2是怎么比较的．

　　映射方法具有效率方面的优势, 因为它把每个对象与单个标量值联系在一起; 排序方法有灵活方面的优势, 它可以在两个对象之间进行任意复杂的比较．排序方法比映射方法的速度慢．













实例:

(1)定义对象类型: TYPE sales_country_t
CREATE TYPE sales_country_t AS OBJECT (
   YEAR              VARCHAR2 (4),
   country           CHAR (2),
   sum_amount_sold   NUMBER
);

(2)定义表类型: TYPE SUM_SALES_COUNTRY_T_TAB
CREATE TYPE sum_sales_country_t_tab AS TABLE OF sales_country_t;

(3)定义对象类型: TYPE sales_gender_t
CREATE TYPE sales_gender_t AS OBJECT (
   YEAR              VARCHAR2 (4),
   country_id        CHAR (2),
   cust_gender       CHAR (1),
   sum_amount_sold   NUMBER
);

(4)定义表类型: TYPE SUM_SALES_GENDER_T_TAB
CREATE TYPE sum_sales_gender_t_tab AS TABLE OF sales_gender_t;



(5) 也可以使用基本类型定义  表类型比如:
1
2
create or replace type test_tab_type as table of varchar2(4000);
/
varchar2(4000) 是一个资本类型,这样相当于定义的表中只有一个字段varchar2(4000)

用法:

TYPE sales_country_t_rec IS RECORD (
      YEAR              VARCHAR (4),
      country           CHAR (2),
      sum_amount_sold   NUMBER
   );
v_sales_country_t_rec sales_country_t_rec;
引用:
v_sales_country_t_rec.year := 'ssss';
v_sales_country_t_rec.country := 'a';
v_sales_country_t_rec.sum_amount_sold := 2 ;
----------------------------------------------------------


1 首先创建一个数据类型
create type t_air  as object(id int ,name varchar(20));
2 创建表
create table aaa(
        id int ,person t_air);
3 插入数据
insert into aaa values(1,t_air(1,'23sdf'));
4 查询classPlace
select a.id ,a.persion.id,a.person.name from aaa a;

t_air(1,'23sdf') 使用这个方式创建一个自定义类型t_air的对象.


三: 下面简单的枚举下常用的几种自定义类型.

1, 子类型.  这种类型最简单, 类似类型的一个别名, 主要是为了对常用的一些类型简单化, 它基于原始的某个类型. 如:  有些应用会经常用到一些货币类型: number(16,2). 如果在全局范围各自定义这种类型, 一旦需要修改该类型的精度, 则需要一个个地修改.  那如何实现定义的全局化呢? 于是就引出了子类型:  subtype cc_num is number(16,2); 这样就很方便地解决了上述的麻烦.
