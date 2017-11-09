---
title: 正则表达式
date: 2016-04-01
tags:
- 正则表达式
- Linux
---

# 正则表达式

    [ab] : 字符a或b
    [a-z] : 所有小写字母
    [A-Z] : 所有大写字母

    + : 次数>=1
    * : 次数>=0
    ? : 次数=0,1
    | : 或
    \* : * 本身
    . : 换行符之外的任何字符
    ^ : 使用该字符的字符之外的所有字符
    ^ : 匹配字符串的开始
    $ : 匹配字符串的结束

## 特殊
    \w : 匹配单词字符，即[a-zA-Z0-9_]
    \W : 非单词字符，匹配[^a-zA-Z0-9_]
    \s : 单个空白字符：space, newline(\n), return(\r), tab(\t), form(\f)，即[ \n\r\t\f\v]
    \S : 相反
    \d : 匹配数字[0-9]
    \D : 匹配[^0-9]

## 例

`\<`表示词首.如`\<abc`表示以 abc 为首的詞
`\>`表示词尾.如`abc\>`表示以 abc 結尾的詞

## 常用函数
常用正则表达式函数主要有:

* re.findall: 返回所有匹配搜索模式的字符串组成的列表；
* re.search(string[, pos[, endpos]]): 搜索字符串直到找到匹配模式的字符串，然后返回一个re.MatchObject对象，否则返回None；
* re.match(string[, pos[, endpos]]): 存在从第一个字符开始的匹配项，存在返回re.MatchObject对象，否则返回None。
* re.sub(repl, string, count=0): 返回repl替换pattern后的字符串。
* re.split: 在pattern出现的地方分割字符串。

re.MatchObject对象主要有以下方法：
* group([group1, …]) : group返回一个或多个分组
* groups([default]) : groups返回包含所有分组的元组


## 正则表达式修饰符 - 可选标志
正则表达式可以包含一些可选标志修饰符来控制匹配的模式。修饰符被指定为一个可选的标志。多个标志可以通过按位 OR(|) 它们来指定。如 re.I | re.M 被设置成 I 和 M 标志：

	re.I	使匹配对大小写不敏感
	re.L	做本地化识别（locale-aware）匹配
	re.M	多行匹配，影响 ^ 和 $
	re.S	使 . 匹配包括换行在内的所有字符
	re.U	根据Unicode字符集解析字符。这个标志影响 \w, \W, \b, \B.
	re.X	该标志通过给予你更灵活的格式以便你将正则表达式写得更易于理解。