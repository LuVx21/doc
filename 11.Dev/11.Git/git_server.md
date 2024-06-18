---
title: git服务器搭建
tags:
- Linux
- Git
---
<!-- TOC -->

- [搭建 GIT 服务器教程](#搭建-git-服务器教程)
	- [安装依赖库和编译工具](#安装依赖库和编译工具)
		- [依赖库](#依赖库)
		- [编译工具](#编译工具)
	- [下载 git](#下载-git)
		- [解压和编译](#解压和编译)
		- [配置环境变量](#配置环境变量)
	- [创建 git 账号密码](#创建-git-账号密码)
	- [初始化 git 仓库并配置用户权限](#初始化-git-仓库并配置用户权限)
	- [配置用户权限](#配置用户权限)
	- [使用搭建好的 Git 服务](#使用搭建好的-git-服务)

<!-- /TOC -->

# 搭建 GIT 服务器教程

@(Git)

[TOC]

## 安装依赖库和编译工具

为了后续安装能正常进行, 我们先来安装一些相关依赖库和编译工具
### 依赖库
```
yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel
```
### 编译工具

```
yum install gcc perl-ExtUtils-MakeMaker
```
## 下载 git

选一个目录, 用来放下载下来的安装包, 这里将安装包放在 /usr/local/src 目录里
```
cd /usr/local/src
```
到官网找一个新版稳定的源码包下载到 /usr/local/src 文件夹里
```
wget https://www.kernel.org/pub/software/scm/git/git-2.10.0.tar.gz
```
### 解压和编译

解压下载的源码包
```
tar -zvxf git-2.10.0.tar.gz
```
解压后进入 git-2.10.0 文件夹
```
cd git-2.10.0
```
执行编译

```
make all prefix=/usr/local/git
```
编译完成后, 安装到 /usr/local/git 目录下

```
make install prefix=/usr/local/git
```
### 配置环境变量


将 git 目录加入 PATH

将原来的 PATH 指向目录修改为现在的目录

```
echo 'export PATH=$PATH:/usr/local/git/bin' >> /etc/bashrc
```
生效环境变量

```
source /etc/bashrc
```
此时我们能查看 git 版本号, 说明我们已经安装成功了.

```
git --version
```
## 创建 git 账号密码

创建 git 账号

为我们刚刚搭建好的 git 创建一个账号

```
useradd -m gituser
```
然后为这个账号设置密码[?]

```
passwd gituser
```

控制台输入创建密码后, 输入您自定义的密码, 并二次确认.

## 初始化 git 仓库并配置用户权限

创建 git 仓库并初始化

我们创建 /data/repositories 目录用于存放 git 仓库

```
mkdir -p /data/repositories
```
创建好后, 初始化这个仓库

```
cd /data/repositories/ && git init --bare test.git
```
## 配置用户权限

给 git 仓库目录设置用户和用户组并设置权限
```
chown -R gituser:gituser /data/repositories
chmod 755 /data/repositories
```
[查找 git-shell 所在目录] , 编辑 /etc/passwd 文件, 将最后一行关于 gituser 的登录 shell 配置改为 git-shell 的目录[?]如下

示例代码:/etc/passwd
```
gituser:x:500:500::/home/gituser:/usr/local/git/bin/git-shell
```
如果按照刚才的步骤执行, 这个位置应该是 /usr/local/git/bin/git-shell, 否则请通过 which git-shell 命令查看位置


安全目的, 限制 git 账号的 ssh 连接只能是登录 git-shell

## 使用搭建好的 Git 服务

克隆 test repo 到本地
````
cd ~ && git clone gituser@<您的 CVM IP 地址>:/data/repositories/test.git
```
实验完成