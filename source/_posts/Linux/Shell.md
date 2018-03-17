---
title: Shell
tags:
- Shell
- Linux
---
<!-- TOC -->

- [Shell](#shell)
- [](#)
    - [terminal 快捷键](#terminal-快捷键)
    - [字符串截取](#字符串截取)
    - [字符串替换](#字符串替换)
- [空值检测](#空值检测)
    - [alias中使用shell函数](#alias中使用shell函数)
    - [(()) 和 []](#-和-)
    - [判断语句](#判断语句)
- [数组](#数组)
    - [TODO](#todo)

<!-- /TOC -->
# Shell

##

`$?`:前一命令的执行结果码

## terminal 快捷键

|例|说明|
|:---|:---|
|`ctrl-a`| 移动光标到行首.|
|`ctrl-e`| 移动光标到行尾.|
|`ctrl-w`| 剪切光标前一个单词（注意是剪切,不是彻底删除,可以通过ctrl-y粘贴.|
|`ctrl-u`| 剪切光标之前的所有内容,如果光标位于行尾,则相当于剪切整行内容.|
|`ctrl-k`| 剪切光标之后的所有内容,有点类似vim的D命令.|
|`ctrl-y`|粘贴剪切的内容.|
|`ctrl-p,n`|向前/向后查看历史命令,和方向键的UP和Down等价.|
|`ctrl-l`| 清屏,相当于执行clear命令,注意不会清除当前行内容.|
|`ctrl-h`| 向前删除一个字符,相当于回退键.|


## 字符串截取

使用例子

str="abbc,def,ghi,abcjkl"

|例|说明|
|:---|:---|
|`echo ${str#a*c}    `|输出`,def,ghi,abcjkl`.(#)表示从左边截取掉最短的匹配 (这里把abbc字串去掉）|
|`echo ${str##a*c}   `|输出jkl,(##)表示从左边截取掉最长的匹配|
|`echo ${str#"a*c"}  `|输出abbc,def,ghi,abcjkl 因为str中没有"a*c"子串|
|`echo ${str##"a*c"} `|输出abbc,def,ghi,abcjkl 同理|
|`echo ${str#*a*c*}  `|空|
|`echo ${str##*a*c*} `|空|
|`echo ${str#d*f)    `|输出abbc,def,ghi,abcjkl,|
|`echo ${str#*d*f}   `|输出,ghi,abcjkl|
|`echo ${str%a*l}    `|`abbc,def,ghi,`一个百分号(%)表示从右边截取最短的匹配|
|`echo ${str%%b*l}   `|`a`两个百分号表示(%%)表示从右边截取最长的匹配|
|`echo ${str%a*c}    `|`abbc,def,ghi,abcjkl`|

> `#`: 左边内容+查找位置
> `%`: 右边内容+查找位置
> 单一符号是最小匹配,两个符号是最大匹配

|例|说明|
|:---|:---|
|`${str:0:5}`||
|`${str:5:5}`||
|`${#str}`||
|`${str: -4}`||

## 字符串替换

str="apple, tree, apple tree"

|例|说明|
|:---|:---|
|`echo ${str/apple/APPLE} `|替换第一次出现的apple|
|`echo ${str//apple/APPLE}`|替换所有apple|
|`echo ${str/#apple/APPLE}`|如果字符串str以apple开头,则用APPLE替换它|
|`echo ${str/%apple/APPLE}`|如果字符串str以apple结尾,则用APPLE替换它|

# 空值检测

file=/dir1/dir2/dir3/my.file.txt

|例|说明|
|:---|:---|
|`echo ${file-my.file.txt}`|unset → my.file.txt 作传回值(空值及非空值时不作处理)|
|`echo ${file:-my.file.txt}`|unset,null →my.file.txt(非空值时不作处理)|
|`echo ${file+my.file.txt}`|null,non-null,均使用 my.file.txt 作传回值(没设定时不作处理)|
|`echo ${file:+my.file.txt}`|non-null → my.file.txt 作传回值 (没设定及空值时不作处理)|
|`echo ${file=my.file.txt}`|unset → my.file.txt 作传回值,同时将 $file 赋值为 my.file.txt  (空值及非空值时不作处理)|
|`echo ${file:=my.file.txt}`|unset,null → my.file.txt 作传回值,同时将 $file 赋值为 my.file.txt  (非空值时不作处理)|
|`echo ${file?my.file.txt}`|unset → my.file.txt 输出至 STDERR (空值及非空值时不作处理)|
|`echo ${file:?my.file.txt}`|unset,null → my.file.txt 输出至 STDERR (非空值时不作处理)|

## alias中使用shell函数

alias中间使用参数,可以使用函数:
```shell
alias src='fun() { grep -i $1 /c/cvs_app/1/src.txt > /c/cvs_app/1/docpath.txt && vs /c/cvs_app/1/docpath.txt;};fun'
## 变异java,删除.class文件
alias jb='fun () { dir=$(dirname $1) && name=$(basename $1 .java) && cd $dir && javac $(basename $1) && java $name && rm $name".class" && cd - > /dev/null;};fun'
```

## (()) 和 []

() : 在子shell中执行
(()): 用于整数计算
$(): 括号内执行结果作为命令
[]: test,多用于if语句
[[]]: 同[],但有更高的容错性


## 判断语句

|运算符|说明|
|:---|:---|
|`-lt`|(<)(less than)|
|`-gt`|(>)(greater than)|
|`-le`|(<=)(less than or equal),|
|`-ge`|(>=)(greater than or equal)|
|`-eq`|(=)|
|`-ne`|(!=)|
|`-nt`|newer than,比较时间戳|
|`-ot`|older than|

> 判断符号两边要有空格

|逻辑符|说明|
|:---|:---|
|`-a`|and|
|`-o`|or|
|`&&`|and|
|`||`|or|


判断字符串或文件属性的常用参数:

|参数|例|说明|
|:---|:---|:---|
|`-z`|`test -z string`|string の文字列長が 0 ならば真となる.|
|`-n`|`test -n string`|string の文字列長が 0 より大ならば真となる.|
|`-d`|`test -d file`|file がディレクトリならば真となる.|
|`-f`|`test -f file`|file が普通のファイルならば真となる.|
|`-s`|`test -s file`|file が 0 より大きいサイズならば真となる.|
|`-e`|`test -e file`|file が存在するならば真となる.|
|`-r`|`test -r file`|file が読み取り可能ならば真となる.|
|`-w`|`test -w file`|file が書き込み可能ならば真となる.|
|`-x`|`test -x file`|file が実行可能ならば真となる.|


# 数组

数组: declare -a arr 或 arr=(1 2 3)
arr[n]=value


关联数组: declare -A map


切片:
`${arr[@]:n:m}` n是offset而m是length
`${map[@]}` 返回索引，相当于keys():

```shell
declare -a arr
declare -A map
arr+=(a b c)
# map+=([a]=1)
map[a]=1
map[b]=2	

echo ${arr[1]}
echo ${map[a]}
# 遍历数组
echo "${arr[@]}"
echo "${map[@]}"
echo "${arr[@]:0:1}"
# 数组长度
echo ${#arr[@]}
# echo "${!map[@]}"

arr[4]=a
echo ${arr[@]}
echo ${arr[3]}
```


## TODO

```
echo off
echo [Start FileCopy] %time% > filecopy.log
FOR /F "tokens=1,2,3 delims=," %%a IN (filecopy.csv) DO (
	copy "%%a" "%%b"
	copy "%%a" "%%c"
	echo pathFrom: %%a → pathTo: %%b >> filecopy.log
	echo pathFrom: %%a → pathTo: %%c >> filecopy.log
	@rem explorer "%%a"
)
echo [End FileCopy] %time% >> filecopy.log
```
```
@echo off

set COPYTO_DIR="S:\Share\3"
set TEMP_DIR=%COPYTO_DIR%_temp
set UPDATE_DIR=%COPYTO_DIR%_update

echo [Start FileCopy] %time% > filecopy.log

FOR /F "tokens=1,2 delims=," %%a IN (filecopy.csv) DO (
	echo ・%%a >> filecopy.log
	copy /Y %%a %UPDATE_DIR%\%%b >> filecopy.log
)

echo [Rename Folder] %time% >> filecopy.log
move %COPYTO_DIR% %TEMP_DIR%
move %UPDATE_DIR% %COPYTO_DIR%
move %TEMP_DIR% %UPDATE_DIR%

echo [End FileCopy] %time% >> filecopy.log
```
