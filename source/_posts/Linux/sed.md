---
title: sed
tags:
- Linux
- Shell
---

# 关于

本身是一个管道命令,主要是以行为单位进行处理,可以将数据行进行替换,删除,新增,选取等特定工作

__格式__

```shell
	sed [option] 'script' <filename>
```

__参数__

|参数|说明|
|:-|:-|
|-n|使用安静(silent)模式.只有经过sed 特殊处理的那一行(或者动作)才会被列出来.|
|-e|直接在指令列模式上进行 sed 的动作编辑;|
|-f|直接将 sed 的动作写在一个档案内, -f filename 则可以执行 filename 内的sed 动作;|
|-r|sed 的动作支援的是延伸型正规表示法的语法.(预设是基础正规表示法语法)|
|-i|直接修改读取的档案内容,而不是由屏幕输出|


# 使用例

__操作指令__

|命令|说明|
|:-|:-|
|a|新增, a 的后面可以接字串,而这些字串会在新的一行出现(目前的下一行)|
|c|替换,行|
|d|删除|
|i|插入, i 的后面可以接字串,而这些字串会在新的一行出现(目前的上一行)|
|p|列印,将某个选择的资料印出.通常 p 会与参数 sed -n 一起|
|s|替换,字符|


## 新增

```shell
# 在第一行后加内容
sed '1a this is added' <filename>
# 在第1~3行后加内容
sed '1,3a this is added' <filename>
# '\n',添加多行
sed '1a this is add' <filename>
# 每行后添加空行
sed 'G' <filename>
```

## 删除


```shell
# 删除1~2行
sed '1,2d' <filename>
# 删除2~最后一行
sed '2,$d' <filename>
# 删除空白行
sed '/^$/d' <filename>
```

## 打印

```shell
# 打印第1行
sed -n '1p' <filename>
# 打印含word的所有行
sed -n '/word/p' <filename>
# 空行
sed -n '/^$/p' <filename>
```

## 替换

__首处替换__

```shell
# 替换每一行的第一处匹配的text
sed 's/text/replace_text/' <filename>
```

__全局替换__

```shell
# 默认替换后，输出替换后的内容，如果需要直接替换原文件,使用`-i`
sed 's/text/replace_text/g' <filename>
```

__变量转换__

```shell
# 已匹配的字符串通过标记&来引用
echo this is en example | sed 's/this/[&]/g'
# [this] is en example
```

__子串匹配标记__

```shell
# TODO
# 第一个匹配的括号内容使用标记 \1 来引用
echo hello1 hello2 | sed 's/hello\([0-9]\)/\1/'
# 1 hello2
```

__双引号求值__

```shell
# sed通常用单引号来引用；使用双引号会对表达式求值
var=ren
echo renxxhaharen | sed "s/$var/HLLOE/"
# HLLOExxhaharen
```

### 替换行

```shell
# 将第1行替换
sed '1c this is replaced' <filename>
# 将第1~3行替换
sed '1,3c this is replaced' <filename>
```

## 插入

```shell
# 每行前3个字符后插入'@'
sed 's/^.\{3\}/&\@/g' <filename>
```


# 参考

[sed命令详解](http://8598676.blog.51cto.com/8588676/1398213)