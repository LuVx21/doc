---
title: awk
date: 2017-09-01
tags:
- Linux
- Shell
---
<!-- TOC -->

- [关于](#关于)
- [命令执行机制](#命令执行机制)
	- [指定多个域分割符](#指定多个域分割符)
- [内置变量](#内置变量)
- [多文件处理](#多文件处理)
- [BEGIN END 关键字](#begin-end-关键字)
- [使用变量](#使用变量)
	- [数学运算](#数学运算)
	- [条件判断](#条件判断)
- [使用正则表达式](#使用正则表达式)

<!-- /TOC -->
# 关于

命令格式和标准Linux命令有稍微的区别, 往往将一些脚本作为参数传进去

```shell
awk [options] 'script' <file>
```

> 脚本放在`''`内, 其中使用`{}`表示一段代码块, 可同时存在多个代码块

# 命令执行机制

awk 以行为处理单位, 读入有'\n'换行符分割的一条记录, 然后将记录按指定的域分隔符划分, 依次执行引号中的各个代码块
文件的每一行中, 由域分隔符分开的每一项称为一个域.默认的域分隔符是空格和制表符
可以使用`-F`指明域分隔符

```
awk -F ':' '{print $1}' /etc/passwd
```

## 指定多个域分割符

```
# 指定`(`和`)`分割, 正则表达式
awk -F '[()]' '{print $1, $2, $3}' log.log
```


# 内置变量

|参数|说明|
|:--|:--|
|$0|当前行全部内容|
|$n|指定第n个域|
|NR|表示文件中的行号, 表示当前是第几行|
|NF|表示文件中的当前行列的个数, 类似于 mysql 数据表里面每一条记录有多少个字段|
|FS|表示 awk 的输入分隔符, 默认分隔符为空格和制表符, 可自定义|
|OFS|表示 awk 的输出分隔符, 默认为空格, 可自定义|
|FILENAME|表示当前文件的文件名称, 如果同时处理多个文件, 它也表示当前文件名称|

例:

fruit.txt:
```
peach    100   Mar  1997   China
Lemon    150   Jan  1986   America
Pear     240   Mar  1990   Janpan
avocado  120   Feb  2008   china
```

```
awk '{print $1, $2, $3}' fruit.txt
```
其中加入的逗号表示插入输出分隔符, 也就是默认的空格

__文件的每一行的每一列的内容除了可以用 print 命令打印输出以外, 还可以对其进行赋值__
```
awk '{$2 = "***"; print $0}' fruit.txt
```

__在参数列表中加入一些字符串或者转义字符等__

```
awk '{print $1 "-" $2 "\t" $3}' fruit.txt
```
加入的字符等, 一定要要在双引号中

__显示行号(NR)__

```
awk '{print NR ":" $0}' fruit.txt
```

__显示域数(NF)__

```
awk '{print NF ":" $0}' fruit.txt
```

__$NR(n行第n个域)__

```
awk '{print $NR}' fruit.txt
```
因为`NR`表示行数, 所以输出第n行第n个域

__$NF(每行最后一个域)__

```
awk '{print $NF}' fruit.txt
```
因为`NF`表示域数目, 所以输出的是最后一个域
对`NF`进行计算可以取第任意个域


# 多文件处理

company.txt:
```
yahoo   100 4500
google  150 7500
apple   180 8000
twitter 120 5000
```

```
awk '{print FILENAME "\t" $0}' fruit.txt company.txt
```

# BEGIN END 关键字

在代码段前面使用`BEGIN`关键字时, 它会在开始读取文件之前运行一次`BEGIN`关键字直接后面的代码段, 
这个代码段只会在读取文件前仅且执行一次
`END`则是在执行完处理文件的命令后, 执行`END`直接后面的代码段, 同样是仅且执行一次

```
awk 'BEGIN {print "Start"} {print FILENAME, $0}' fruit.txt company.txt
```
```
awk 'BEGIN {print "Start read file"} {print $0} END {print "End file"}' fruit.txt company.txt
```

# 使用变量

```shell
awk '{msg="hello world"; print msg}' /etc/passwd
awk 'BEGIN {msg="hello world"} {print msg}' /etc/passwd
```
> 声明的变量可以跨代码段使用

## 数学运算

同样的其中还支持`+`, `-`, `*`, `/`, `%`等数学运算
```
awk '{a = 12; b = 24; print a + b}' company.txt
```


## 条件判断

对域值判断能起到筛选的作用
```
awk '$3 < 5500 {print $0}' company.txt
```

> 支持的判断操作还有`<`, `>`, `<=`, `>=`, `==`, `!=`, `~`, `!~`
> `~`:匹配正则表达式, `!~`:不匹配
> 还可以使用`if`关键字

# 使用正则表达式

```
awk '/word/{print $0}' <filename>
```

> `/`和`/`之间为正则表达式匹配模式

```

# 参考

[详解 awk 工具的使用方法](https://my.oschina.net/jarly/blog/898144)
