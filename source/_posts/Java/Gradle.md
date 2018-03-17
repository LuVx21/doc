---
title: Gradle
date: 2018-01-01
tags:
- Gradle
---
<!-- TOC -->

- [命令](#命令)
- [依赖](#依赖)
    - [作用域](#作用域)
- [模块化](#模块化)
- [Gradle转Maven](#gradle转maven)
- [Maven转Gradle](#maven转gradle)
- [tomcat](#tomcat)

<!-- /TOC -->
# 命令

|参数|说明|
|:---|:---|
|`-b`|指定构建文件|
|`-x`|忽略某个任务|
|`--continue`|出错不停止构建|

# 依赖

build.gradle文件中:
```
// 版本号声明可写在其中
ext {}
// url 可自定义仓库位置
repositories {}
dependencies {}
```

## 作用域

compile:编译
runtime:运行时
testCompile:测试
testRuntime:包含以上

# 模块化

```
# 项目模块结构
gradle -q projects
```

# Gradle转Maven

```gradle
apply plugin: 'java'
apply plugin: 'maven'
 
group = 'org.luvx'
version = '1.0-SNAPSHOT'
 
dependencies {
    compile 'commons-lang:commons-lang:2.3'
}
```

```shell
gradle install
```

# Maven转Gradle

```shell
gradle init --type pom
```

# tomcat

gradle tomcatRun
gradle tomcatRunWar