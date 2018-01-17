---
title: git branch/tag
date: 2018-01-16
tags:
- Git
---
<!-- TOC -->

- [branch](#branch)
- [tag](#tag)

<!-- /TOC -->

# branch

```
git push origin --delete <branchName>
```


# tag

```
git tag <tagname> -m 'message'
git push --tags
git fetch origin tag <tagname>
```
```
git tag -d <tagname>
git push origin --delete tag <tagname>
```