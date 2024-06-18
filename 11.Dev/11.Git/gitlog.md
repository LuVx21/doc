<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [日志](#日志)
- [表结构](#表结构)
- [解析](#解析)
- [阅读](#阅读)

<!-- /TOC -->
</details>

git log 转为 表数据 sql查询

## 日志

```bash
git log --pretty=format:'|%h|%ae|%an|%aI|%s' --numstat > commits.log
git config --global core.quotepath false
git ls-files > files.log
```

```diff
|482c386|scott.xie@trustlife.com|scott.xie|2020-09-04T09:54:31+08:00|看板图片压缩
5	5	core/src/main/java/org/luvx/ddp/app/little/dashboard/task/DashboardTask3.java
```

## 表结构

```sql
CREATE TABLE IF NOT EXISTS commits (
    id	TEXT UNIQUE,
    summary	TEXT,
    author_name	TEXT,
    author_email	TEXT,
    author_when	DATETIME
);

CREATE TABLE IF NOT EXISTS commit_files (
    id    TEXT,
    name  TEXT,
    added INT,
    deleted INT
);

create table if not exists current_files (
    name TEXT
);
```

## 解析


## 阅读

https://willschenk.com/articles/2020/gitlog_in_sqlite/
