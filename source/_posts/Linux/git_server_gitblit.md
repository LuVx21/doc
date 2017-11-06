---
title: gitblit-git服务器搭建
tags:
- Linux
- Git
---

# 搭建git服务器

## windows下

1. 新建文件夹dir1作为版本库文件夹
2. 修改`data/gitblit.properties`文件(5点):
参考`defaults.properties`文件

    ```
    # Base folder for repositories.
    # This folder may contain bare and non-bare repositories but Gitblit will only
    # allow you to push to bare repositories.
    # Use forward slashes even on Windows!!
    # e.g. c:/gitrepos
    #
    # SINCE 0.5.0
    # RESTART REQUIRED
    # BASEFOLDER

    # 上面创建的存放代码的目录
    git.repositoriesFolder = E:\git\gitdata

    # Standard http port to serve.  <= 0 disables this connector.
    # On Unix/Linux systems, ports < 1024 require root permissions.
    # Recommended value: 80 or 8080
    #
    # SINCE 0.5.0
    # RESTART REQUIRED

    # 设置访问的端口,不要和别的端口有冲突
    server.httpPort = 1000

    # Secure/SSL https port to serve. <= 0 disables this connector.
    # On Unix/Linux systems, ports < 1024 require root permissions.
    # Recommended value: 443 or 8443
    #
    # SINCE 0.5.0
    # RESTART REQUIRED
    server.httpsPort = 1001

    # Specify the interface for Jetty to bind the standard connector.
    # You may specify an ip or an empty value to bind to all interfaces.
    # Specifying localhost will result in Gitblit ONLY listening to requests to
    # localhost.
    #
    # SINCE 0.5.0
    # RESTART REQUIRED

    # 搭建服务器的IP
    server.httpBindInterface =192.168.1.109

    # Specify the interface for Jetty to bind the secure connector.
    # You may specify an ip or an empty value to bind to all interfaces.
    # Specifying localhost will result in Gitblit ONLY listening to requests to
    # localhost.
    #
    # SINCE 0.5.0
    # RESTART REQUIRED

    # localhost就行
    server.httpsBindInterface =localhost
    ```

3. 修改`data\installService.cmd`文件:安装服务

    ```
    @REM arch = x86, amd64, or ia32
    SET ARCH=x86
    SET CD=D:\Git\gitblit-1.7.1(你的实际目录)
    # StartParams里的启动参数,给空
    ```

4. 启动服务
5. 设置防火墙规则,允许局域网访问
6. 配置用户等