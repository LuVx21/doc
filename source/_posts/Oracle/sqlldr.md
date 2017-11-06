---
title: Oracle sqlldr
date: 2017-03-15
tags:
- Oracle
---

# 1.sqlldr介绍

使用sqlldr导入文本数据到oracle

用法:
	sqlldr keyword=value [,keyword=value,...]

有效的关键字:

* userid -- ORACLE 用户名/口令
* control -- 控制文件名
* log -- 日志文件名
* bad -- 错误文件名
* data -- 数据文件名
* discard -- 废弃文件名
* discardmax -- 允许废弃的文件的数目 (全部默认)
* skip -- 要跳过的逻辑记录的数目 (默认 0)
* load -- 要加载的逻辑记录的数目 (全部默认)
* errors -- 允许的错误的数目 (默认 50)
* rows -- 常规路径绑定数组中或直接路径保存数据间的行数(默认:常规路径 64, 所有直接路径)
* bindsize -- 常规路径绑定数组的大小 (以字节计) (默认 256000)
* silent -- 运行过程中隐藏消息 (标题,反馈,错误,废弃,分区)
* direct -- 使用直接路径 (默认 FALSE)
* parfile -- 参数文件: 包含参数说明的文件的名称
* parallel -- 执行并行加载 (默认 FALSE)
* file -- 要从以下对象中分配区的文件
* skip_unusable_indexes -- 不允许/允许使用无用的索引或索引分区 (默认 FALSE)
* skip_index_maintenance -- 没有维护索引, 将受到影响的索引标记为无用 (默认 FALSE)
* commit_discontinued -- 提交加载中断时已加载的行 (默认 FALSE)
* readsize -- 读取缓冲区的大小 (默认 1048576)
* external_table -- 使用外部表进行加载; NOT_USED, GENERATE_ONLY, EXECUTE (默认 NOT_USED)
* columnarrayrows -- 直接路径列数组的行数 (默认 5000)
* streamsize -- 直接路径流缓冲区的大小 (以字节计) (默认 256000)
* multithreading -- 在直接路径中使用多线程
* resumable -- 启用或禁用当前的可恢复会话 (默认 FALSE)
* resumable_name -- 有助于标识可恢复语句的文本字符串
* resumable_timeout -- RESUMABLE 的等待时间 (以秒计) (默认 7200)
* date_cache -- 日期转换高速缓存的大小 (以条目计) (默认 1000)
* no_index_errors -- 出现任何索引错误时中止加载 (默认 FALSE)


NOTE: 命令行参数可以由位置或关键字指定
前一种的例子是
'sqlldr scott/tiger foo'
后一种情况的一个示例是
'sqlldr userid=scott/tiger control=foo'

位置指定参数的时间必须早于但不可迟于由关键字指定的参数.
例如
允许'sqlldr scott/tiger control=foo logfile=log',
但是
不允许'sqlldr scott/tiger control=foo log',
即使参数'log'的位置正确.

# 例

```sql
sqlldr [userid=]user/pwd@dbname control=text.ctl direct=true rows=1000000 discard=text.dsc log=text.log
```
text.ctl:
```sql
options(load=-1,skip=1,errors=1,rows=1000000)
load data characterset utf8
infile 'text.csv'
badfile 'error.bad'
truncate into table user.tablename
fields terminated by "," 
optionally enclosed by '"'
trailing nullcols
(
"CHAR1"  NULLIF ("CHAR1"= 'null'),
"CHAR2",
"CHAR3",
"CHAR4"
)
```

> replace into table user.tablename <--> truncate into table user.tablename
> into table user.tablename append