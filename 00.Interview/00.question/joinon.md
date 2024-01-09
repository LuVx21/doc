---
title: 面试题:sql分析
date: 2018-09-17
tags:
- MySQL
---
表a:

| id   | name |
| :--- | :--- |
| 1    | 1    |
| 2    | 2    |
| 3    | 3    |

表b:

| id   | name | a_id |
| :--- | :--- | :--- |
| 1    | 1    | 1    |
| 2    | 2    | 2    |

```sql
select a.name, b.name
from a left join b on a.id = b.a_id and a.id = 1
```

结果:
| name | name |
| :--- | :--- |
| 1    | 1    |
| 2    |      |
| 3    |      |

```sql
select a.name, b.name
from a left join b on a.id = b.a_id
where a.id = 1
```
结果:
| name | name |
| :--- | :--- |
| 1    | 1    |

对于`left join`, 无论on后面的语句真假, 表a的数据都会被全部拿出来,

换句话说, on后的筛选条件不会影响结果的条数, 只会影响被join的表的抽出结果.

因此, 如果使用了left, right, 查询的目的就是想要那些未完全关联的数据, 此时筛选条件放在on后面, 

否则(放在where后面)left和right不起任何作用.


