---
title: Gradle
date: 2018-01-01
tags:
- Gradle
---
<!-- TOC -->

- [Gradle转Maven](#gradle转maven)
- [Maven转Gradle](#maven转gradle)

<!-- /TOC -->

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