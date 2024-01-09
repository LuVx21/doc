---
title: impdp_expdp
date: 2017-10-18 22:08:29
tags:
---

<!-- TOC -->

- [impdp_expdp](#impdp_expdp)
    - [backup dir](#backup-dir)
    - [expdp](#expdp)
    - [impdp](#impdp)
    - [parfile](#parfile)

<!-- /TOC -->

# impdp_expdp

1, exp和imp是客户端工具程序, 它们既可以在客户端使用, 也可以在服务端使用.
2, expdp和impdp是服务端的工具程序, 他们只能在oracle服务端使用, 不能在客户端使用.
3, imp只适用于exp导出的文件, 不适用于expdp导出文件; impdp只适用于expdp导出的文件, 而不适用于exp导出文件.
4, 对于10g以上的服务器, 使用exp通常不能导出0行数据的空表, 而此时必须使用expdp导出.

## backup dir

一, 创建逻辑目录, 该命令不会在操作系统创建真正的目录, 最好以system等管理员创建.
```
create directory dpdata1 as 'd:\test\dump';
```
二, 查看管理理员目录(同时查看操作系统是否存在, 因为oracle并不关心该目录是否存在, 如果不存在, 则出错)
```
select * from dba_directories;
```
三, 给scott用户赋予在指定目录的操作权限, 最好以system等管理员赋予.
```
grant read, write on directory dpdata1 to scott;
```

## expdp
```
expdp scott/tiger key=value
```

|参数|说明|
|:---|:---|
|userid=scott/tiger@XE||
|schemas=scott||
|tables=emp||
|query='where deptno=20'||
|tablespaces=temp||
|dumpfile=expdp.dmp||
|directory=dpdata1||
|parallel=40|并行进程|
|full=y|整个数据库|


> parallel 参数为导出使用一个以上的线程来显著地加速作业.每个线程创建一个单独的转储文件, 因此参数 dumpfile 应当拥有和并行度一样多的项目.您可以指定通配符作为文件名

```
expdp scott/tiger tables=cases directory=dpdata1 dumpfile=expcases_%u.dmp parallel=4 job_name=cases_export
```

例:
并行进程parallel
```
expdp scott/tiger@orcl directory=dpdata1 dumpfile=scott3.dmp parallel=40 job_name=scott3
```

## impdp
```
impdp scott/tiger key=value
```

|参数|说明|
|:---|:---|
|remap_schema||
|table_exists_action||

例:

改变表的owner
```
impdp system/manager directory=dpdata1 dumpfile=expdp.dmp tables=scott.dept remap_schema=scott:system;
```

追加数据
```
impdp system/manager directory=dpdata1 dumpfile=expdp.dmp schemas=system 
```

## parfile

可以将命令的参数等写入一个文件中, 命令中使用`parfile=xx.par`来代替其他的参数

> parfile的其中一个最显著的应用是query参数的在不同OS下的转义