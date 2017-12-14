---
title: Nexus搭建Maven私服
date: 2017-08-12
tags:
- Maven
- Nexus
---


nexus.bat install
nexus.bat uninstall
nexus.bat start

1.	hosted，宿主仓库，部署自己的jar到这个类型的仓库，包括releases和snapshot两部分，Releases公司内部发布版本仓库、 Snapshots 公司内部测试版本仓库

2.	proxy，代理仓库，用于代理远程的公共仓库，如maven中央仓库，用户连接私服，私服自动去中央仓库下载jar包或者插件。 

3.	group，仓库组，用来合并多个hosted/proxy仓库，通常我们配置自己的maven连接仓库组。
4.	virtual(虚拟)：兼容Maven1 版本的jar或者插件 
