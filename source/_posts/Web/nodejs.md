---
title: nodejs
tags:
- nodejs
---
<!-- TOC -->

- [关于](#关于)
    - [模块化](#模块化)
- [install](#install)
- [package](#package)
- [npm](#npm)
- [文件系统](#文件系统)
- [fs](#fs)

<!-- /TOC -->

# 关于

* node是一款对ES标准实现的JS引擎
* 通过node可以使js在服务器中运行
* node就是一款使用js编写的web服务器
* node底层是使用c++的编写的
* node的中js引擎使用的chrome的v8引擎

node的特点：

1. 非阻塞、异步的I/O
2. 事件和回调函数
3. 单线程（主线程单线程，后台I/O线程池）
4. 跨平台


- Node仅仅对ES标准进行了实现，所以在Node中不包含DOM 和 BOM
- Node中可以使用所有的内建对象
    String Number Boolean Math Date RegExp Function Object Array
    而BOM和DOM都不能使用
        但是可以使用 console 也可以使用定时器（setTimeout() setInterval()）

- Node可以在后台来编写服务器
    Node编写服务器都是单线程的服务器

- 传统的服务器都是多线程的
    - 每进来一个请求，就创建一个线程去处理请求

- Node的服务器单线程的
    - Node处理请求时是单线程，但是在后台拥有一个I/O线程池

## 模块化

- ES5中没有原生支持模块化，我们只能通过script标签引入js文件来实现模块化
- 在node中为了对模块管理，引入了CommonJS规范

- 模块的引用
    - 使用 require()函数来引入一个模块
    - 例子：
        var 变量 = require("模块的标识");

- 模块的定义
    - 在node中一个js文件就是一个模块
    - 默认情况下在js文件中编写的内容，都是运行在一个独立的函数中，
        外部的模块无法访问
    - 导出变量和函数
        - 使用 exports
            - 例子：
                exports.属性 = 属性值;
                exports.方法 = 函数;

        - 使用module.exports
            - 例子：
                module.exports.属性 = 属性值;
                module.exports.方法 = 函数;
                module.exports = {};

- 模块的标识
    - 模块的标识就是模块的名字或路径
        我们node通过模块的标识来寻找模块的
        对于核心模块（npm中下载的模块），直接使用模块的名字对其进行引入
            var fs = require("fs");
            var express = require("express");

        对于自定义的文件模块，需要通过文件的路径来对模块进行引入
            路径可以是绝对路径，如果是相对路径必须以./或 ../开头
            var router = require("./router");

# install

```shell
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
# wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.2/install.sh | bash
nvm install stable
```

# package

- 将多个模块组合为一个完整的功能，就是一个包
- 包结构
    bin
        - 二进制的可执行文件，一般都是一些工具包中才有
    lib
        - js文件
    doc
        - 文档
    test
        - 测试代码
    package.json
        - 包的描述文件

- package.json
    - 它是一个json格式的文件，在它里面保存了包各种相关的信息
        name 包名
        version 版本
        dependencies 依赖
        main 包的主要的文件
        bin 可执行文件

# npm

Node Package Manager

```shell
# 查看npm的版本
npm -v
# 查看所有模块的版本
npm version
# 初始化项目（创建package.json）
npm init
# 搜索包
npm s/search 包名
# 安装指定的包
npm i/install 包名
# 安装指定的包并添加依赖
npm i/install 包名 --save
# 全局安装（一般都是一些工具）
npm i/install 包名 -g
# 安装当前项目所依赖的包
npm i/install
# 删除一个包
npm r/remove 包名
# 安装包并添加到依赖中
npm install 包名 --save
# 下载当前项目所依赖的包
npm install
# 全局安装包（全局安装的包一般都是一些工具）
npm install 包名 -g
```

# 文件系统

- Buffer（缓冲区）
    - Buffer和数组的结构的非常类似，Buffer是用来存储二进制数据的
    - Buffer的方法
        - Buffer.from(字符串)
            - 将一个字符串中内容保存到一个buffer中
        - buf.toString()
            - 将buffer转换为一个字符串
        - Buffer.alloc(size)
            - 创建一个指定大小的buffer对象
        - Buffer.allocUnsafe(size)
            - 创建一个指定大小的buffer对象，可以包含敏感数据

# fs

- 在Node通过fs模块来对系统中的文件进行操作，fs模块是node中已经继承好了，不需要在使用npm下载，直接引入即可
- 引入fs
    var fs = require("fs");
- fs模块中的大部分操作都提供了两种方法，同步方法和异步方法
    同步方法带sync
    异步方法没有sync，都需要回调函数

- 写入文件
    1.同步写入
    2.异步写入
    3.简单写入
    4.流式写入

- 读取文件
    1.同步读取
    2.异步读取
    3.简单读取
    4.流式读取

- 方法
    - 打开文件
        fs.open(path, flags[, mode], callback)
        fs.openSync(path, flags[, mode])

    - 读写文件
        fs.write(fd, string[, position[, encoding]], callback)
        fs.writeSync(fd, string[, position[, encoding]])

        fs.read(fd, buffer, offset, length, position, callback)
        fs.readSync(fd, buffer, offset, length, position)

    - 关闭文件
        fs.close(fd,callback)
        fs.closeSync(fd);

    - 简单文件读取和写入
        fs.writeFile(file, data[, options], callback)
        fs.writeFileSync(file, data[, options])

        fs.readFile(path[, options], callback)
        fs.readFileSync(path[, options])


    - 流式文件读取和写入
        - 流式读取和写入适用于一些比较大的文件
            fs.createWriteStream(path[, options])
            fs.createReadStream(path[, options])
