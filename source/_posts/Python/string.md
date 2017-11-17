---
title: Pyhton String 手册
tags:
- Python
---
<!-- TOC -->

- [字符串](#字符串)
- [字符串合并](#字符串合并)
- [字符串截取](#字符串截取)
- [常见的字符串操作的函数](#常见的字符串操作的函数)
    - [字符串中字符大小写的变换:](#字符串中字符大小写的变换)
    - [字符串在输出时的对齐:](#字符串在输出时的对齐)
    - [字符串中的搜索和替换:](#字符串中的搜索和替换)
    - [字符串的分割和组合:](#字符串的分割和组合)
    - [字符串类型判断:](#字符串类型判断)

<!-- /TOC -->

###字符串

在字符串中插入%开头的字符,如果%后面跟的是s则表示是字符.

	world = "world"
	print("Hello, %s" % world)
如果是数字,则%后面跟的是d,同时会自动四舍五入,去掉小数点之后的数据.

	str1 = "version"
	nu = 1.0
	format = "%s %d" % (str, nu)
	print(format)
浮点型后面跟的f默认情况下会保留六位小数,也可以指定小数点位数.

	print("浮点型: %f" % 1.25)
	print("浮点型保留一位小数: %.1f" % 1.25)
	print("浮点型保留两位小数: %.2f" % 1.254)
还可以通过字典的形式格式化字符串.

	print("%(version)s: %(num).1f" % {"version": "version", "num": 2})
	也可以调换version和num的顺序而不影响输出
	print("%(version)s: %(num).1f" % {"num":2,"version": "version"})

###字符串合并

Python使用"+"直接连接字符串,如果两边都是字符串就进行字符串连接,如果两边都是数字就进行 加法运算,如果两边的数据类型不一样就会抛异常.

	str1 = "hello "
	str2 = "world "
	str3 = "hello "
	str4 = "xinxingzhao"
	result = str1 + str2 + str3
	result += str4
	print(result)
还有一种合并字符串的方式是用join函数.

	strs = ['hello ', 'world ', 'hello ', 'xinxingzhao']
	result = "".join(strs)
	print(result)
如果我们采用Python的连接操作的这种功能的话就显得很高大上了哈.这里我们需要用到一个reduce 的函数.

	from functools import reduce
	import operator
	str = ['hello','world','renxie','LuVx']
	result = reduce(operator.add,str,'')
	print result
	
###字符串截取

通过索引获得字符串中的字符

	word = 'xinxngzhao'
	print(word[3])
截取子字符串的时候就可以用切片的方法,语法格式如下

	string[start : end : step]
string表示被截取的字符串,start表示截取起点,end表示截取终点, step表示每次截多少个字符,可以理解为每step个字符截取一个首字符.

	str1 = 'hello xinxingzhao'
	print(str1[0:3])
	print(str1[::2])
	print(str1[1::2])
除了用切片的办法,还有就是用split函数,语法如下

	split([char] [, num])
char表示用于分割的字符,默认情况下是以空格作为分割字符,num表示分割多少次, 默认情况会以char在字符串中出现的次数来进行分割的.

	sentence = "xinxingzhao said: 1, 2, 3, 4"
	print("使用空格进行分割:", sentence.split())
	print("使用逗号进行分割:", sentence.split(","))
	print("使用两个逗号进行分割:", sentence.split(",", 2))
注意: 字符串进行连接,分割,切片之后,原字符串保持不变,Python会给新的字符串 重新分配空间.可以通过下面的代码进行验证.

	str1 = "a"
	print(id(str1))
	print(id(str1 + 'b'))

###常见的字符串操作的函数

####字符串中字符大小写的变换:
	S.lower() #小写
	S.upper() #大写
	S.swapcase() #大小写互换
	S.capitalize() #首字母大写

####字符串在输出时的对齐:

	S.ljust(width,[fillchar])   #输出width个字符,S左对齐,不足部分用fillchar填充,默认的为空格.
	S.rjust(width,[fillchar])   #右对齐
	S.center(width, [fillchar]) #中间对齐
	S.zfill(width):把S变成width长,并在右对齐,不足部分用0补足

####字符串中的搜索和替换:
	S.find(substr, [start, [end]])
	#返回S中出现substr的第一个字母的标号,如果S中没有substr则返回-1.start和end作用就相当于在S[start:end]中搜索
	S.index(substr, [start, [end]])
	#与find()相同,只是在S中没有substr时,会返回一个运行时错误
	S.rfind(substr, [start, [end]])
	#返回S中最后出现的substr的第一个字母的标号,如果S中没有substr则返回-1,也就是说从右边算起的第一次出现的substr的首字母标号
	S.rindex(substr, [start, [end]])
	S.count(substr, [start, [end]])   #计算substr在S中出现的次数
	S.replace(oldstr, newstr, [count])
	#把S中的oldstar替换为newstr,count为替换次数.这是替换的通用形式,还有一些函数进行特殊字符的替换
	S.strip([chars]):#把S中前后chars中有的字符全部去掉,可以理解为把S前后chars替换为None
	S.lstrip([chars])
	S.rstrip([chars])
	S.expandtabs([tabsize]):#把S中的tab字符替换没空格,每个tab替换为tabsize个空格,默认是8个

####字符串的分割和组合:

	S.split([sep, [maxsplit]]) #以sep为分隔符,把S分成一个list.maxsplit表示分割的次数.默认的分割符为空白字符
	S.rsplit([sep, [maxsplit]])
	S.splitlines([keepends])   #把S按照行分割符分为一个list,keepends是一个bool值,如果为真每行后而会保留行分割符.
	S.join(seq):#把seq代表的序列──字符串序列,用S连接起来

####字符串类型判断:

	S.isalnum() #是否全是字母和数字,并至少有一个字符
	S.isalpha() #是否全是字母,并至少有一个字符
	S.isdigit() #是否全是数字,并至少有一个字符
	S.isspace() #是否全是空白字符,并至少有一个字符
	S.islower() #S中的字母是否全是小写
	S.isupper() #S中的字母是否便是大写
	S.istitle() #S是否是首字母大写的 