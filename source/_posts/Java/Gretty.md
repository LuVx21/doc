---
title: Gretty
date: 2018-02-02
tags:
- Gradle
---

<!-- TOC -->

- [命令](#命令)

<!-- /TOC -->
#

```
apply from: 'https://raw.github.com/akhikhl/gretty/master/pluginScripts/gretty.plugin'
gretty {
  // jetty7/8/9，tomcat7/8
  servletContainer = 'tomcat8'
}
```

```
buildscript {
    repositories {
         jcenter()
    }
    dependencies {
         classpath 'org.akhikhl.gretty:gretty:+'
    }
}

apply plugin: 'org.akhikhl.gretty'
```

# 命令

```
gradle appRun
appRunDebug
appRunWar
appRunWarDebug
```
```
gradle appStart
appStartDebug
appStartWar
appStartWarDebug
```

> `jetty*`和`tomcat*`命令,使用同上