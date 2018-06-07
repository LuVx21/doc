---
title: git branch/tag
date: 2018-01-16
tags:
- Git
---
<!-- TOC -->

- [标签:tag](#标签tag)
- [分支:branch](#分支branch)
- [储藏:stash](#储藏stash)
- [](#)
- [分析](#分析)

<!-- /TOC -->

# 标签:tag

```shell
# git tag -a <tagname> -m "message"
git tag <tagname> -m 'message'
# git push origin -tags
git push --tags
git fetch origin tag <tagname>
```
```shell
git tag -d <tagname>
# git push origin :refs/tags/version
git push origin --delete tag <tagname>
```
```shell
# git tag -l
git tag
```

# 分支:branch

```shell
git push origin --delete <branchName>
git push origin :<branchName>
```
# 储藏:stash

```shell
git stash
# 查看stash
git stash list
# 应用stash
git stash apply [<stashid>]
# 暂存区的仍然回到暂存区
git stash apply --index
# 移除stash
git stash drop [<stashid>]
# 类似于apply,但会从stash栈中移除applied stash
git stash pop

# 取消储藏
git stash show -p [<stashid>] | git apply -R

# alias:git stash-unapply
git config --global alias.stash-unapply '!git stash show -p | git apply -R'
# 基于stash创建分支
git stash branch <branch_name>
```

# 

git blame -L 10,+2 request.js

# 分析

统计某人的代码提交量，包括增加，删除:
 
git log --author="$(git config --get user.name)" --pretty=tformat: --numstat | gawk '{ add += $1 ; subs += $2 ; loc += $1 - $2 } END { printf "added lines: %s removed lines : %s total lines: %s\n",add,subs,loc }' -

仓库提交者排名前 5（如果看全部，去掉 head 管道即可）:

git log --pretty='%aN' | sort | uniq -c | sort -k1 -n -r | head -n 5

仓库提交者（邮箱）排名前 5:这个统计可能不会太准，因为很多人有不同的邮箱，但会使用相同的名字

git log --pretty=format:%ae | gawk -- '{ ++c[$0]; } END { for(cc in c) printf "%5d %s\n",c[cc],cc; }' | sort -u -n -r | head -n 5

贡献者统计:

git log --pretty='%aN' | sort -u | wc -l

提交数统计:

git log --oneline | wc -l 

添加或修改的代码行数:

git log --stat|perl -ne 'END { print $c } $c += $1 if /(\d+) insertions/;





git log 参数说明:

--author 指定作者

--stat 显示每次更新的文件修改统计信息，会列出具体文件列表

--shortstat 统计每个commit 的文件修改行数，包括增加，删除，但不列出文件列表: 

--numstat 统计每个commit 的文件修改行数，包括增加，删除，并列出文件列表:



-p 选项展开显示每次提交的内容差异，用 -2 则仅显示最近的两次更新

例如:git log -p -2

--name-only 仅在提交信息后显示已修改的文件清单

--name-status 显示新增、修改、删除的文件清单

--abbrev-commit 仅显示 SHA-1 的前几个字符，而非所有的 40 个字符

--relative-date 使用较短的相对时间显示（比如，“2 weeks ago”）

--graph 显示 ASCII 图形表示的分支合并历史

--pretty 使用其他格式显示历史提交信息。可用的选项包括 oneline，short，full，fuller 和 format（后跟指定格式）

例如: git log --pretty=oneline ; git log --pretty=short ; git log --pretty=full ; git log --pretty=fuller

--pretty=tformat: 可以定制要显示的记录格式，这样的输出便于后期编程提取分析

例如:git log --pretty=format:""%h - %an, %ar : %s""

下面列出了常用的格式占位符写法及其代表的意义。 

选项 说明 

%H 提交对象（commit）的完整哈希字串 

%h 提交对象的简短哈希字串 

%T 树对象（tree）的完整哈希字串 

%t 树对象的简短哈希字串 

%P 父对象（parent）的完整哈希字串 

%p 父对象的简短哈希字串 

%an 作者（author）的名字 

%ae 作者的电子邮件地址 

%ad 作者修订日期（可以用 -date= 选项定制格式） 

%ar 作者修订日期，按多久以前的方式显示 

%cn 提交者(committer)的名字 

%ce 提交者的电子邮件地址 

%cd 提交日期 

%cr 提交日期，按多久以前的方式显示 

%s 提交说明 

--since 限制显示输出的范围，

例如: git log --since=2.weeks 显示最近两周的提交

选项 说明 

-(n) 仅显示最近的 n 条提交 

--since, --after 仅显示指定时间之后的提交。 

--until, --before 仅显示指定时间之前的提交。 

--author 仅显示指定作者相关的提交。 

--committer 仅显示指定提交者相关的提交。


一些例子: git log --until=1.minute.ago // 一分钟之前的所有 log git log --since=1.day.ago //一天之内的log git log --since=1.hour.ago //一个小时之内的 log git log --since=`.month.ago --until=2.weeks.ago //一个月之前到半个月之前的log git

log --since ==2013-08.01 --until=2013-09-07 //某个时间段的 log git blame

看看某一个文件的相关历史记录

例如:git blame index.html --date short

