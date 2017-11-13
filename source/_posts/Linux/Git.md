---
title: Git
date: 2017-10-17 22:08:29
tags:
- Linux
- Git
---
<!-- TOC -->

- [Git](#git)
	- [状态](#状态)
	- [git clean](#git-clean)
	- [删除:git rm](#删除git-rm)
	- [移动:git mv](#移动git-mv)
	- [历史:git log](#历史git-log)
		- [常规查看（最常用）](#常规查看最常用)
		- [格式化显示](#格式化显示)
		- [提交历史搜索](#提交历史搜索)
	- [撤销](#撤销)
		- [撤销修改](#撤销修改)
			- [撤销工作区](#撤销工作区)
			- [撤销暂存区](#撤销暂存区)
			- [撤销暂存区和工作区](#撤销暂存区和工作区)
		- [撤销Commit](#撤销commit)
			- [修改提交日志](#修改提交日志)
		- [版本回退](#版本回退)
		- [撤销远程](#撤销远程)
	- [配置提交模板](#配置提交模板)

<!-- /TOC -->
# Git

@(Linux)

## 状态

git目前下仅存在三种空间状态:
1. 工作空间
2. 暂存空间
3. 版本库

版本化的文件有且仅有3中状态:
1. 提交
2. 修改
3. 暂存

工作空间下的文件仅有2中状态:
1. 版本化
2. 未版本化


## git clean


```
# 显示能被清除的文件
git clean -n
# 清除为版本化的文件,依赖`.gitignore`
git clean -f <path>
# 不依赖`.gitignore`,
git clean -xf
# 清除为版本化的文件夹
git clean -d
```
常搭配reset以回退状态

## 删除:git rm

```
git rm
```
直接删除,不在工作空间留下文件
如果有处于工作空间或暂存区的修改(未commit更改),则会执行失败

```
git rm -f
```
不对是否有未commit更改做检查,
直接删除,不在工作空间留下文件

```
git rm --cached <filename>
```
直接删除,但会在工作空间留下文件
若同时有工作空间和暂存区的修改时,执行失败
仅有一种时,正常结束

## 移动:git mv

可以用于重命名

## 历史:git log

### 常规查看（最常用）

--graph：图形化显示分支提交历史
--oneline：一个提交显示一行
--decorate：显示分支引用

```
git log --graph --oneline --decorate
```

### 格式化显示

%h：Commit对象的简短哈希串
%t：Tree对象的简短哈希串
%p：父Commit对象的简短哈希串
%an：作者名字
%ae：作者邮件
%ad：修订日期
%s：Commit Message

```
git log --pretty=format:"%h %t %p %an %s" --graph
```


### 提交历史搜索

--grep：搜索提交说明
--author：匹配作者
--committer：匹配提交者
--after：时间起点
--before：时间终点
--：特定路径

```
git log --oneline --decorate --graph --grep=C --author=zhongmingmao --committer=xx \
--after=2017-01-01 --before=2018-01-01 -- .
```

## 撤销

`checkout`:

* 撤销修改

`reset`:

* 撤销修改
* 版本回退

> 撤销还是版本回退和`HEAD`有关

### 撤销修改

#### 撤销工作区

```
git checkout -- <file>
```
撤销工作区的修改

#### 撤销暂存区

```
git reset HEAD <file>
```
撤销已经提交至暂存区的修改(撤销git add)

#### 撤销暂存区和工作区

1.
```
git checkout HEAD .
```
2.
```
git reset --hard HEAD
```
撤销处于工作区和暂存区的所有修改
会强制覆盖暂存区和工作目录中的所有修改

> 危险操作

### 撤销Commit

> git commit --amend会重新生成新的Commit对象

#### 修改提交日志
```
git commit --amend -m 'messgae'
```
* 修改提交message
* 合并提交+修改提交日志

> 不同在于work tree 是否存在可提交内容

### 版本回退

```
git reset head^
git reset --mixed head^
```
版本回退,上2个命令等价,其实`--mixed`是默认参数
会把暂存区的修改退回到工作目录(相当于撤销git add操作)

`--soft`:仅会撤销回退目标点后的提交存在暂存区
`--mixed`:会撤销回退目标点后的提交和原本处于暂存的修改存在工作区
`--hard`:不管是否有未提交的修改,直接全部覆盖更改


### 撤销远程

`git revert`

使用时工作空间和暂存区没有未提交的修改
本质是用一个新提交来消除一个历史提交所做的任何修改
首先revert后本地版本库会退到历史版本,然后push操作则可以更新远程版本库,这一步不会像reset那样出现冲突问题
reset 是在正常的commit历史中,删除了指定的commit,这时 HEAD 是向前移动了,而 revert 是在正常的commit历史中再commit一次,只不过是反向提交,他的 HEAD 是一直向前的.


> revert和reset回退时的区别?

## 配置提交模板

```
当前分支的提交模板
git config commit.template xx.xx
全局的提交模板
git config -–global commit.template xx.xx
git config –-global core.editor vim
```


> 参考:

[Pro Git Ed1](http://iissnan.com/progit/)