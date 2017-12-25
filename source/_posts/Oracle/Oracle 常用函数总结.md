---
title: Oracle 常用函数
date: 2017-05-05
tags:
- Oracle
---

<!-- TOC -->

- [rank()&dense_rank()&row_number](#rankdense_rankrow_number)

<!-- /TOC -->


# rank()&dense_rank()&row_number

`rank()`排序时,并列的则序号相同,但会占用序号后的顺序
`dense_rank()`排序时,并列的则序号相同,不会占用序号后的顺序
`row_number()`不考虑并列,即使并列也不影响序号
```
rank() over(partition by deptno order by sal desc) as rank
dense_rank() over(partition by deptno order by sal desc) as rank
row_number() over(partition by deptno order by sal desc) as rank
```
`partition by`用于给结果集进行分区,对数据排序(记录数不变)
`group  by`原始数据进行聚合统计(记录数可能变少, 每组返回一条)
`rank() over`针对`null`作最大处理,排在最前,从而影响排序结果

---

```
select floor(3.25) from dual;
<=>select trunc(3.25,0) from dual;

select ceil(3.25) from dual;
<=>
--实现四舍五入
select floor(3.25 + 0.5) from dual;
```
```
nvl(name,'renxie')

nvl2(name,'isnotnull','isnull')
```
```
decode(name,'if1','result1','if2','result2'.......)

coalesce(value1,value2,value3,.......)
```
```
select trunc(sysdate,'yyyy') from dual; --返回当年第一天
select trunc(sysdate,'mm') from dual; --返回当月第一天
select trunc(sysdate,'d') from dual; --返回当前星期的第一天
select trunc(78.657,2) from dual;
```
```
select instr('hello world! llo!','llo',1,1) from dual;--子串位置,index从0开始
--sql server中使用charindex()函数
charindex(expression1,expression2,[start_location])
```
```
select replace('hello world!','wor','xieren') from dual;
```
```
select trim(' hello ') from dual;
```
```
--实现行列转换 decode()
select
sum(decode(col1,1,'',3,''))
,sum(decode(col2,2,'',4,''))
from
tables
;
```
```
--实现行列转换 pivot()
--year|quarter|amount
select
year as 年份, q1 as 一季度, q2 as 二季度, q3 as 三季度, q4 as 四季度
from
income
pivot
(
sum(amount)
for
quarter in (q1, q2, q3, q4)
) as p
order by year desc

--employeeid|week|income
select
--这里是pivot第三步（选择行转列后的结果集的列）这里可以用“*”表示选择所有列，也可以只选择某些列(也就是某些天)
[星期一],[星期二],[星期三],[星期四],[星期五],[星期六],[星期日]
from
--这里是pivot第二步骤(准备原始的查询结果，因为pivot是对一个原始的查询结果集进行转换操作，
--所以先查询一个结果集出来)这里可以是一个select子查询，但为子查询时候要指定别名，否则语法错误
week_income
pivot
(
--这里是pivot第一步骤，也是核心的地方，进行行转列操作。聚合函数sum表示你需要怎样处理转换后的列的值，是总和(sum)，还是平均(avg)还是min,max等等。
--例如如果week_income表中有两条数据并且其week都是“星期一”，其中一条的income是1000,另一条income是500，那么在这里使用sum，行转列后“星期一”这个列的值当然是1500了。
--后面的for [week] in([星期一],[星期二])中 for [week]就是说将week列的值分别转换成一个个列，也就是“以值变列”。但是需要转换成列的值有可能有很多，
--我们只想取其中几个值转换成列，那么怎样取呢？就是在in里面了，比如我此刻只想看工作日的收入，在in里面就只写“星期一”至“星期五”（注意，in里面是原来week列的值,"以值变列"）。
--总的来说，sum(income) for [week] in([星期一],[星期二],[星期三],[星期四],[星期五],[星期六],[星期日])这句的意思如果直译出来，
--就是说：将列[week]值为"星期一","星期二","星期三","星期四","星期五","星期六","星期日"分别转换成列，这些列的值取income的总和。
  sum(income) for [week] in([星期一],[星期二],[星期三],[星期四],[星期五],[星期六],[星期日])
) as tbl--别名一定要写

--实现列行转换 unpivot()
create table pvt (
vendorid int,
emp1 int,
emp2 int,
  emp3 int,
  emp4 int,
  emp5 int
);
go
insert into pvt values (1,4,3,5,4,4);
insert into pvt values (2,4,1,5,5,5);
insert into pvt values (3,4,3,5,4,4);
insert into pvt values (4,4,2,5,5,4);
insert into pvt values (5,5,1,5,5,5);
go
--unpivot the table.
select
vendorid, employee, orders
from
(
  select vendorid, emp1, emp2, emp3, emp4, emp5
  from pvt
) p
unpivot
(
  orders for employee in (emp1, emp2, emp3, emp4, emp5)
)as unpvt;
go

unpivot的输入是左表表达式p，第一步，先为p中的行生成多个副本，在unpivot中出现的每一列，都会生成一个副本。
因为这里的in子句有5个列名称，所以要为每个来源行生成5个副本。结果得到的虚拟表中将新增一个列，
用来以字符串格式保存来源列的名称（for和in之间的，上面例子是 employee ）。
第二步，根据新增的那一列中的值从来源列中提取出与列名对应的行。第三步，删除掉结果列值为null的行，完成这个查询。
```
----------------------------------------------------------------------------------------------------------------------------------------
```sql
--一个存储过程
create or replace procedure go_class(p_cur in out sys_refcursor)
as
  l_query_sql varchar2(4000) := 'select sname ';
begin
  for t_rec in (select distinct subject from t)
  loop
  l_query_sql := l_query_sql || replace(',sum(decode(subject,''xx$'',fraction,null)) xx$','xx$',dbms_assert.simple_sql_name(t_rec.subject) );
  end loop;

  l_query_sql := l_query_sql || ' from t group by sname';

  if not p_cur%isopen
  then
  open p_cur for l_query_sql;
  end if;
end go_class;
/

--编译存储过程
alter procedure go_class compile
--??
variable x refcursor
--执行存储过程
exec go_class(:x);
```
> http://blog.csdn.net/lanchunhui/article/details/51505671
> http://www.cnblogs.com/advocate/p/3729998.html