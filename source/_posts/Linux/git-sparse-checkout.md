---
title: Git检出版本库部分内容
tags: 
- Git
- Linux
---
<!-- TOC -->

- [检出内容](#检出内容)
- [修改检出目录](#修改检出目录)
- [关闭部分检出功能](#关闭部分检出功能)
- [子目录的匹配](#子目录的匹配)

<!-- /TOC -->

# 检出内容

有时候项目过大, 而我们平时也只会用到其中的一部分内容, 这时候可以只取出其中需要常用的部分.

Git的这个功能被称为:sparse checkout(稀疏检出)

首先需要创建空的版本库

```shell
git config core.sparsecheckout true
echo '/source/_posts/*' >> .git/info/sparse-checkout
echo '/scaffolds/*' >> .git/info/sparse-checkout
git remote add origin git@github.com:luvx21/hexo.git
git pull origin master
```

如果没有检出所有更改的必要, 可以指定`depth`参数
```shell
# 取最近一次的变更
git pull --depth=1 origin master
```

# 修改检出目录

修改了`.git/info/sparse-checkout`, 
如增加或删除部分目录, 可以执行如下命令重新Checkout

```
git checkout master
```
或者
```
git read-tree -mu HEAD
```

# 关闭部分检出功能

关闭`sparse checkout`功能时, 仅仅修改配置, 
```
git config core.sparsecheckout false
```
是不生效的, 需要修改`.git/info/sparse-checkout`文件, 用一个`*`号替代其中的内容, 
然后重新checkout(`checkout`, `read-tree`)

# 子目录的匹配

在 sparse-checkout 文件中, 
如果目录名称前带斜杠, 如`/docs/`, 将只匹配项目根目录下的`docs`目录, 
如果目录名称前不带斜杠, 如`docs/`, 其他目录下如果也有这个名称的目录, 如`test/docs/`也能被匹配.
而如果写了多级目录, 如`docs/05/`, 则不管前面是否带有斜杠, 都只匹配项目根目录下的目录, 如`test/docs/05/`不能被匹配.
