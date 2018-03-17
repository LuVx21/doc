---
title: Pyhton 基础手册
tags:
- Python
---
<!-- TOC -->

- [Python基础](#python基础)
    - [注释](#注释)
    - [表达式](#表达式)
    - [位运算:](#位运算)
                - [break](#break)
                - [continute](#continute)
    - [list列表](#list列表)
        - [列表的读取](#列表的读取)
        - [删除列表中的值](#删除列表中的值)
        - [Python列表脚本操作符](#python列表脚本操作符)
        - [Python列表函数&方法](#python列表函数方法)
            - [Python 包含以下函数:](#python-包含以下函数)
            - [Python 包含以下方法:](#python-包含以下方法)
    - [Tuple元组](#tuple元组)
        - [访问元组](#访问元组)
        - [元组运算符](#元组运算符)
        - [元组索引&截取](#元组索引截取)
        - [无关闭分割符](#无关闭分割符)

<!-- /TOC -->
# Python基础

@(Python)

[toc]

* Python, 是一种面向对象、解释型计算机程序设计语言, 由Guido van Rossum于1989年发明, 第一个公开发行版发行于1991年.
* Python是纯粹的自由软件, 源代码和解释器CPython遵循 GPL(GNU General Public License)协议 .
* Python语法简洁清晰, 特色之一是强制用空白符(white space)作为语句缩进.
* Python具有丰富和强大的库.它常被昵称为胶水语言, 能够把用其他语言制作的各种模块(尤其是C/C++)很轻松地联结在一起.常见的一种应用情形是, 使用Python快速生成程序的原型(有时甚至是程序的最终界面), 然后对其中有特别要求的部分, 用更合适的语言改写, 比如3D游戏中的图形渲染模块, 性能要求特别高, 就可以用C/C++重写, 而后封装为Python可以调用的扩展类库.需要注意的是在您使用扩展类库时可能需要考虑平台问题, 某些可能不提供跨平台的实现.


## 注释
* 单行注释
	使用#符号
* 多行注释
	'''  '''
	"""  """ 即三个单引号或双引号, 中间为注释代码
* 中文处理
	需要在文件开头加上中文注释, 不声明则会默认使用ASKII码保存
	#coding=utf-8或#--coding:UTF-8--
## 表达式
幂运算:a**b  a的b次幂
除且向下取整:a//b
	

| 表达式|说明|类型|
| :--- | :---| :--- |
|not a|a的逻辑非|bool|
|a and b|a和b的逻辑与|bool|
|a or b|a和b的逻辑或|bool|
|a is b|a和b是同一个对象|bool|
|a is not b|a和b是不同对象|bool|
> 不等符号:!= 和 <> (废弃)
## 位运算:

| 表达式|说明|
| :--- | :---|
|a << n|左移n位|
|a >> n|右移n位|
|a & b|位与|
|a \| b|位或|
|a ^ b|位异或|
条件语句

```
if (a>b):
	print a
elif a == b:
	print a==b
else:
	print b
```

循环语句
```
while循环
	while count < 5:
	    print (count, 'is less than 5')
	    count = count + 1
	else:
	    print (count, 'is not less than 5')
```
for循环:
```
for letter in 'Python':
    print letter
fruits = ['apple', 'banana', 'cat', 'dog']
for i in fruits:
    print i
```

#####break
和C中相似, 能够打破最小封闭for或while循环
#####continute
和break不同, continue语句跳出本次循环, 当前循环中的剩余语句跳过执行, 而break跳出整个循环.
##list列表
可以简单地将列表理解为C中的数组, 列表和元组都能够保存任意类型的python对象.
* 列表元素用[], 元组元素使用()括起来.
* 通过截取[] [:]能够得到子集.此操作同于字符串相关操作, 基本样式[start:end:length].

以下即使list的定义:
		
	aList = ['a', 'b', 'b']
###列表的读取

    >> str = ['a', 'b', 'c', 'd']
    >> str[2]   # 'c'
    >> str[-2]  # 'c'
    >> str[1:]   # ['b', 'c', 'd']
###删除列表中的值

    >> aList = [1, 2, 3, 4, 5]
    >> del aList[3]
    >> [1, 2, 3, 5]
###Python列表脚本操作符
列表对+和*操作符的支持与字符串类似.+用于拼接列表, *用于重复列表.
| Python表达式 |     结果 |   描述   |
| :--- | ---:| :---: |
| len([1, 2, 3])|   3|  length|
|['a']*2|['a', 'a']|重复列表|
|for x in [1, 2, 3] print x|1 2 3|迭代|
### Python列表函数&方法
####Python 包含以下函数:
| No.| 函数|   说明|
| :---: | :---| :---: |
| 1|   cmp(list1, list2)|  比较两个列表的元素|
|2|len(list)|列表长度|
|3|max(list)|列表最大值|
|4|min(list)|列表最小值|
|5|list(seq)|将元组转换为列表|
####Python 包含以下方法:
| No.| 方法|   说明|
| :---: | :---| :--- |
|1|list.append(obj)|在末尾添加新的对象|
|2|list.count(obj)|统计某元素出现的次数|
|3|list.extend(seq)|在末尾一次性添加另一个序列的多个值(用新列表扩展原列表)|
|4|list.index(obj)|查找对象的索引值|
|5|list.insert(index, obj)|将对象插入列表|
|6|list.pop(obj=list[-1])|移除列表的一个元素(默认最后一个), 并返回该元素的值|
|7|list.remove(obj)|移除某个值的第一个匹配项|
|8|list.reverse()|所有元素反向|
|9|list.sort([func])|排序|
##Tuple元组
元组和列表类似, 不同的就是元组的元素不能够修改, 也不能删除, 也可以进行截取和拼接操作.

    aTuple = ('a', 'b', 'c')
###访问元组
	>> aTuple[2]
	'c'
###元组运算符
也可以使用+和*进行运算.
###元组索引&截取
元组本质上也是一个序列, 因此可以访问指定位置的元素, 也可以截取其中一段元素.
###无关闭分割符
任意无符号的对象, 以逗号隔开, 默认为元组.

	>> print 'abc', -4.24e93, 18+6.6j, 'xyz'
	abc -4.24e+93 (18+6.6j) xyz
	>> x, y = 1, 2
	>> print "Value of x , y : ", x, y
	Value of x , y :  1 2