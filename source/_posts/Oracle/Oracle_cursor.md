---
title: Oracle Cursor
date: 2017-04-25
tags:
- Oracle
---

<!-- TOC -->


<!-- /TOC -->

```sql
-- 声明游标; CURSOR cursor_name IS select_statement

--For 循环游标
--(1)定义游标
--(2)定义游标变量
--(3)使用for循环来使用这个游标
declare
	--类型定义
	cursor c_job
	is
	select empno,ename,job,sal
	from emp
	where job='MANAGER';
	--定义一个游标变量v_cinfo c_emp%ROWTYPE , 该类型为游标c_emp中的一行数据类型
	c_row c_job%rowtype;
begin
	for c_row in c_job loop
	dbms_output.put_line(c_row.empno||'-'||c_row.ename||'-'||c_row.job||'-'||c_row.sal);
	end loop;
end;
```
```sql
--Fetch游标
--使用的时候必须要明确的打开和关闭

declare
--类型定义
cursor c_job
is
select empno,ename,job,sal
from emp
where job='MANAGER';
--定义一个游标变量
c_row c_job%rowtype;
begin
open c_job;
loop
--提取一行数据到c_row
fetch c_job into c_row;
--判读是否提取到值, 没取到值就退出
--取到值c_job%notfound 是false
--取不到值c_job%notfound 是true
exit when c_job%notfound;
dbms_output.put_line(c_row.empno||'-'||c_row.ename||'-'||c_row.job||'-'||c_row.sal);
end loop;
--关闭游标
close c_job;
end;
```
```sql
--1: 任意执行一个update操作, 用隐式游标sql的属性%found,%notfound,%rowcount,%isopen观察update语句的执行情况.
begin
update emp set ENAME='ALEARK' WHERE EMPNO=7469;
if sql%isopen then
dbms_output.put_line('Openging');
else
dbms_output.put_line('closing');
end if;
if sql%found then
dbms_output.put_line('游标指向了有效行');--判断游标是否指向有效行
else
dbms_output.put_line('Sorry');
end if;
if sql%notfound then
dbms_output.put_line('Also Sorry');
else
dbms_output.put_line('Haha');
end if;
dbms_output.put_line(sql%rowcount);
exception
when no_data_found then
dbms_output.put_line('Sorry No data');
when too_many_rows then
dbms_output.put_line('Too Many rows');
end;
```
```sql
declare
empNumber emp.EMPNO%TYPE;
empName emp.ENAME%TYPE;
begin
if sql%isopen then
dbms_output.put_line('Cursor is opinging');
else
dbms_output.put_line('Cursor is Close');
end if;
if sql%notfound then
dbms_output.put_line('No Value');
else
dbms_output.put_line(empNumber);
end if;
dbms_output.put_line(sql%rowcount);
dbms_output.put_line('-------------');

select EMPNO,ENAME into empNumber,empName from emp where EMPNO=7499;
dbms_output.put_line(sql%rowcount);

if sql%isopen then
dbms_output.put_line('Cursor is opinging');
else
dbms_output.put_line('Cursor is Closing');
end if;
if sql%notfound then
dbms_output.put_line('No Value');
else
dbms_output.put_line(empNumber);
end if;
exception
when no_data_found then
dbms_output.put_line('No Value');
when too_many_rows then
dbms_output.put_line('too many rows');
end;



--2,使用游标和loop循环来显示所有部门的名称
--游标声明
declare
cursor csr_dept
is
--select语句
select DNAME
from Depth;
--指定行指针,这句话应该是指定和csr_dept行类型相同的变量
row_dept csr_dept%rowtype;
begin
--for循环
for row_dept in csr_dept loop
dbms_output.put_line('部门名称:'||row_dept.DNAME);
end loop;
end;


--3,使用游标和while循环来显示所有部门的的地理位置(用%found属性)
declare
--游标声明
cursor csr_TestWhile
is
--select语句
select LOC
from Depth;
--指定行指针
row_loc csr_TestWhile%rowtype;
begin
--打开游标
open csr_TestWhile;
--给第一行喂数据
fetch csr_TestWhile into row_loc;
--测试是否有数据, 并执行循环
while csr_TestWhile%found loop
dbms_output.put_line('部门地点: '||row_loc.LOC);
--给下一行喂数据
fetch csr_TestWhile into row_loc;
end loop;
close csr_TestWhile;
end;
select * from emp




--4,接收用户输入的部门编号, 用for循环和游标, 打印出此部门的所有雇员的所有信息(使用循环游标)
--CURSOR cursor_name[(parameter[,parameter],...)] IS select_statement;
--定义参数的语法如下:Parameter_name [IN] data_type[{:=|DEFAULT} value]

declare
CURSOR
c_dept(p_deptNo number)
is
select * from emp where emp.depno=p_deptNo;
r_emp emp%rowtype;
begin
for r_emp in c_dept(20) loop
dbms_output.put_line('员工号: '||r_emp.EMPNO||'员工名: '||r_emp.ENAME||'工资: '||r_emp.SAL);
end loop;
end;
select * from emp
--5: 向游标传递一个工种, 显示此工种的所有雇员的所有信息(使用参数游标)
declare
cursor
c_job(p_job nvarchar2)
is
select * from emp where JOB=p_job;
r_job emp%rowtype;
begin
for r_job in c_job('CLERK') loop
dbms_output.put_line('员工号'||r_job.EMPNO||' '||'员工姓名'||r_job.ENAME);
end loop;
end;
SELECT * FROM EMP

--6: 用更新游标来为雇员加佣金: (用if实现,创建一个与emp表一摸一样的emp1表, 对emp1表进行修改操作),并将更新前后的数据输出出来
--http://zheng12tian.iteye.com/blog/815770
create table emp1 as select * from emp;

declare
cursor
csr_Update
is
select * from emp1 for update OF SAL;
empInfo csr_Update%rowtype;
saleInfo emp1.SAL%TYPE;
begin
FOR empInfo IN csr_Update LOOP
IF empInfo.SAL<1500 THEN
saleInfo:=empInfo.SAL*1.2;
elsif empInfo.SAL<2000 THEN
saleInfo:=empInfo.SAL*1.5;
elsif empInfo.SAL<3000 THEN
saleInfo:=empInfo.SAL*2;
END IF;
UPDATE emp1 SET SAL=saleInfo WHERE CURRENT OF csr_Update;
END LOOP;
END;

--7:编写一个PL/SQL程序块, 对名字以‘A’或‘S’开始的所有雇员按他们的基本薪水(sal)的10%给他们加薪(对emp1表进行修改操作)
declare
cursor
csr_AddSal
is
select * from emp1 where ENAME LIKE 'A%' OR ENAME LIKE 'S%' for update OF SAL;
r_AddSal csr_AddSal%rowtype;
saleInfo emp1.SAL%TYPE;
begin
for r_AddSal in csr_AddSal loop
dbms_output.put_line(r_AddSal.ENAME||'原来的工资:'||r_AddSal.SAL);
saleInfo:=r_AddSal.SAL*1.1;
UPDATE emp1 SET SAL=saleInfo WHERE CURRENT OF csr_AddSal;
end loop;
end;
--8: 编写一个PL/SQL程序块, 对所有的salesman增加佣金(comm)500
declare
cursor
csr_AddComm(p_job nvarchar2)
is
select * from emp1 where JOB=p_job FOR UPDATE OF COMM;
r_AddComm emp1%rowtype;
commInfo emp1.comm%type;
begin
for r_AddComm in csr_AddComm('SALESMAN') LOOP
commInfo:=r_AddComm.COMM+500;
UPDATE EMP1 SET COMM=commInfo where CURRENT OF csr_AddComm;
END LOOP;
END;

--9: 编写一个PL/SQL程序块, 以提升2个资格最老的职员为MANAGER(工作时间越长, 资格越老)
--(提示: 可以定义一个变量作为计数器控制游标只提取两条数据; 也可以在声明游标的时候把雇员中资格最老的两个人查出来放到游标中.)
declare
cursor crs_testComput
is
select * from emp1 order by HIREDATE asc;
--计数器
top_two number:=2;
r_testComput crs_testComput%rowtype;
begin
open crs_testComput;
FETCH crs_testComput INTO r_testComput;
while top_two>0 loop
dbms_output.put_line('员工姓名: '||r_testComput.ENAME||' 工作时间: '||r_testComput.HIREDATE);
--计速器减一
top_two:=top_two-1;
FETCH crs_testComput INTO r_testComput;
end loop;
close crs_testComput;
end;


--10: 编写一个PL/SQL程序块, 对所有雇员按他们的基本薪水(sal)的20%为他们加薪, 
--如果增加的薪水大于300就取消加薪(对emp1表进行修改操作, 并将更新前后的数据输出出来)
declare
cursor
crs_UpadateSal
is
select * from emp1 for update of SAL;
r_UpdateSal crs_UpadateSal%rowtype;
salAdd emp1.sal%type;
salInfo emp1.sal%type;
begin
for r_UpdateSal in crs_UpadateSal loop
salAdd:= r_UpdateSal.SAL*0.2;
if salAdd>300 then
salInfo:=r_UpdateSal.SAL;
dbms_output.put_line(r_UpdateSal.ENAME||': 加薪失败. '||'薪水维持在: '||r_UpdateSal.SAL);
else
salInfo:=r_UpdateSal.SAL+salAdd;
dbms_output.put_line(r_UpdateSal.ENAME||': 加薪成功.'||'薪水变为: '||salInfo);
end if;
update emp1 set SAL=salInfo where current of crs_UpadateSal;
end loop;
end;

--11:将每位员工工作了多少年零多少月零多少天输出出来
--近似
--CEIL(n)函数: 取大于等于数值n的最小整数
--FLOOR(n)函数: 取小于等于数值n的最大整数
--truc的用法 http://publish.it168.com/2005/1028/20051028034101.shtml
declare
cursor
crs_WorkDay
is
select ENAME,HIREDATE, trunc(months_between(sysdate, hiredate) / 12) AS SPANDYEARS,
trunc(mod(months_between(sysdate, hiredate), 12)) AS months,
trunc(mod(mod(sysdate - hiredate, 365), 12)) as days
from emp1;
r_WorkDay crs_WorkDay%rowtype;
begin
for r_WorkDay in crs_WorkDay loop
dbms_output.put_line(r_WorkDay.ENAME||'已经工作了'||r_WorkDay.SPANDYEARS||'年,零'||r_WorkDay.months||'月,零'||r_WorkDay.days||'天');
end loop;
end;

--12:输入部门编号, 按照下列加薪比例执行(用CASE实现, 创建一个emp1表, 修改emp1表的数据),并将更新前后的数据输出出来
-- deptno raise(%)
-- 10 5%
-- 20 10%
-- 30 15%
-- 40 20%
-- 加薪比例以现有的sal为标准
--CASE expr WHEN comparison_expr THEN return_expr
--[, WHEN comparison_expr THEN return_expr]... [ELSE else_expr] END
declare
cursor
crs_caseTest
is
select * from emp1 for update of SAL;
r_caseTest crs_caseTest%rowtype;
salInfo emp1.sal%type;
begin
for r_caseTest in crs_caseTest loop
case
when r_caseTest.DEPNO=10
THEN salInfo:=r_caseTest.SAL*1.05;
when r_caseTest.DEPNO=20
THEN salInfo:=r_caseTest.SAL*1.1;
when r_caseTest.DEPNO=30
THEN salInfo:=r_caseTest.SAL*1.15;
when r_caseTest.DEPNO=40
THEN salInfo:=r_caseTest.SAL*1.2;
end case;
update emp1 set SAL=salInfo where current of crs_caseTest;
end loop;
end;

--13:对每位员工的薪水进行判断, 如果该员工薪水高于其所在部门的平均薪水, 则将其薪水减50元, 输出更新前后的薪水, 员工姓名, 所在部门编号.
--AVG([distinct|all] expr) over (analytic_clause)
---作用:
--按照analytic_clause中的规则求分组平均值.
--分析函数语法:
--FUNCTION_NAME(<argument>,<argument>...)
--OVER
--(<Partition-Clause><Order-by-Clause><Windowing Clause>)
--PARTITION子句
--按照表达式分区(就是分组),如果省略了分区子句,则全部的结果集被看作是一个单一的组
select * from emp1
DECLARE
CURSOR
crs_testAvg
IS
select EMPNO,ENAME,JOB,SAL,DEPNO,AVG(SAL) OVER (PARTITION BY DEPNO ) AS DEP_AVG
FROM EMP1 for update of SAL;
r_testAvg crs_testAvg%rowtype;
salInfo emp1.sal%type;
begin
for r_testAvg in crs_testAvg loop
if r_testAvg.SAL>r_testAvg.DEP_AVG then
salInfo:=r_testAvg.SAL-50;
end if;
update emp1 set SAL=salInfo where current of crs_testAvg;
end loop;
end;
```