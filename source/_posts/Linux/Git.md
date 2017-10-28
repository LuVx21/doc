---
title: Git
date: 2017-10-17 22:08:29
tags: Linux
---

# Git

@(Linux)

## 

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
从Working Tree和Index中移除
必须进行up-to-date检查，如果文件或目录在Working Tree或Index中的状态与HEAD不一致，则执行失败


```
git rm -f
```
强制从Working Tree和Index中移除，不进行up-to-date检查

```
git rm –cached
```

只从Index中移除，保留Working Tree中的文件状态

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

### 撤销Commit

> git commit --amend会重新生成新的Commit对象

#### 修改提交日志
```
git commit --amend -m 'messgae'
```
* 修改提交message
* 合并提交+修改提交日志

> 不同在于work tree 是否存在可提交内容

### 撤销Index

> `git reset HEAD` == `git reset --mixed HEAD`，用HEAD覆盖Index


### 撤销Working Tree

> `git checkout --`，用Index覆盖Working Tree

### 撤销Index和Working Tree


> * git reset --hard HEAD
> * git checkout HEAD [filename]
> 
> 危险操作，将会丢失上次Commit后的所有修改，用HEAD覆盖Index和Working Tree


### 配置提交模板
```
当前分支的提交模板
git config commit.template xx.xx
全局的提交模板
git config -–global commit.template xx.xx
git config –-global core.editor vim
```