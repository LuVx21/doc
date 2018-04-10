---
title: SQL调优
date: 2017-05-02
tags:
- SQL
- 调优
---
<!-- TOC -->

- [调优方案](#调优方案)

<!-- /TOC -->

# 调优方案

01. 必须选择记录条数最少的表作为基础表.
  from 是从前往后检索的，所以要最少记录的表放在最前面
02. 采用自下而上的顺序解析WHERE子句,根据这个原理,表之间的连接必须写在其他WHERE条件之前, 那些可以过滤掉最大数量记录的条件必须写在WHERE子句的末尾.同时在链接的表中能过滤的就应该先进行过滤.
  where是从后往前检索，所以能过滤最多数据的条件应放到最后.
03. SELECT子句中避免使用 `*`
04. 尽量多使用COMMIT
05. 计算记录条数时候，第一快：count(索引列)，第二快：cout(*)
06. 用Where子句替换HAVING子句
07. 通过内部函数提高SQL效率
08. 使用表的别名(Alias)
09. 用EXISTS替代IN
10. 用NOT EXISTS替代NOT IN
11. 用表连接替换EXISTS
12. 用索引提高效率
13. 避免在索引列上使用计算（此条包括在select后面  where后面等任何地方，因为在索引列上计算会导致索引失效）
14. 避免在索引列上使用NOT（在索引列使用not会导致索引失效）
15. 用>=替代>
16. 用UNION替换OR (适用于索引列)
17. 用IN来替换OR
18. 避免在索引列上使用IS NULL和IS NOT NULL
19. 总是使用索引的第一个列
20. 用UNION-ALL 替换UNION ( 如果有可能的话)
21. ORDER BY 子句只在两种严格的条件下使用索引.
22. 避免改变索引列的类型
23. 需要当心的WHERE子句
24. 避免使用耗费资源的操作（带有DISTINCT,UNION,MINUS,INTERSECT,ORDER BY）



explain plan for sql;
SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY());
