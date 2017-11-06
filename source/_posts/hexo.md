---
title: hexo
date: 2017/10/02 20:46:25
tags:
- hexo
- node.js
---

```
npm install -g hexo-cli
hexo init <folder>
cd <folder>
npm install
```

项目结构

```
.
├── _config.yml:配置文件
├── package.json:应用程序信息
├── scaffolds:模板文件
├── source
|   ├── _drafts
|   └── _posts
└── themes
```

```
hexo new [layout] filename
# Run server
hexo server
# Generate static files
hexo generate
hexo publish [layout] filename
# Deploy to remote sites
hexo deploy
```


清除缓存文件 (db.json) 和已生成的静态文件 (public).
```
hexo clean
```

列出网站资料.
```
hexo list <type>
```

显示 Hexo 版本.
```
hexo version
```

## draft

这种布局在建立时会被保存到 `source/_drafts` 文件夹,您可通过 `publish` 命令将草稿移动到 `source/_posts` 文件夹
```
hexo publish [layout] filename
```