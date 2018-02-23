---
title: git branch/tag
date: 2018-01-16
tags:
- Git
---
<!-- TOC -->

- [分支](#分支)
- [标签](#标签)

<!-- /TOC -->

# 分支

```
git push origin --delete <branchName>
git pus origin :<branchName>
```

# 标签

```
git tag <tagname> -m 'message'
git push --tags
git fetch origin tag <tagname>
```
```
git tag -d <tagname>
git push origin --delete tag <tagname>
```