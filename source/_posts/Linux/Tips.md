---
title: Linux 下的一些特殊需求
date: 2017-08-27
tags:
- Linux
- Shell
---

<!-- TOC -->

- [提取目录和文件名的方法](#提取目录和文件名的方法)
    - [使用${}](#使用)
    - [basename和dirname](#basename和dirname)
- [按行读取文件内容](#按行读取文件内容)

<!-- /TOC -->

# 提取目录和文件名的方法

## 使用${}

```
var=/a/b/1.txt
${var##*/}
```
去掉变量var从左边算起的最后一个'/'字符及其左边的内容，返回从左边算起的最后一个'/'（不含该字符）的右边的内容

```
var=/a/b/1.tar.gz
${var#*.}
```
去掉变量var从左边算起的第一个'.'字符及其左边的内容，返回从左边算起第一个'.'（不含该字符）的右边部分的内容

```
${var##*/}
${var#*/}
${var%/*}
${var%%/*}
```
总结:
`#`:从左边算起
`%`:从右边算起
上述符号出现1次:从其算起方向开始的第一个匹配处
上述符号出现2次:从其算起方向开始的最后一个匹配处
`*`:位于`/`,`.`字符的哪一边则表示哪一边的内容抛弃掉


## basename和dirname

```
var=/a/b/1.txt
echo $(basename $var) # 提取文件名(含扩展名)
echo $(basename $var .txt) # (不含扩展名)
dirname $var # 提取目录,也能提取出目录所在目录
```

# 按行读取文件内容

```
# 使用read命令
cat path.txt | while read line
do
    echo "File:${line}"
done

while read line
do 
    echo "File:${line}"
done < path.txt
```

```
# 使用循环
for line in $(cat data.dat)
do 
    echo "File:${line}"
done

for line in `cat data.dat`
do 
    echo "File:${line}"
done
```

```
# 使用awk命令
cat pets.txt | awk '{for(i=2;i<NF;i++) {printf $i} printf "\n"}'
```