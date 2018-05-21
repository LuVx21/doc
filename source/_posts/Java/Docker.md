---
title: Docker
date:
tags:
- Docker
---

<!-- TOC -->

- [基本命令](#基本命令)
- [使用MySQL](#使用mysql)

<!-- /TOC -->

# 基本命令

仓库名：`<用户名>/<软件名>`,缺省用户名则默认是library(官方镜像)。

```shell
# docker pull library/ubuntu:16.04 # 例
docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
# 运行
docker run
# 操作镜像
docker image [ls|rm]
# 操作容器
docker container [start|stop|restart|ls|rm]
# 进入容器
docker exec -it <id|name> bash
```

# 使用MySQL

```shell
docker pull mysql:5.6
docker run --name <name> -e MYSQL\_ROOT\_PASSWORD=<pwd> -d mysql:<tag>
docker run -it --rm mysql mysql -h<host> -u<user> -p
# 例
# docker run --name test-mysql -p 127.0.0.1:3307:3306 -e MYSQL\_ROOT\_PASSWORD=1121 -d mysql:5.6
# mysql -h 127.0.0.1 -P3307 -u root -p
```
