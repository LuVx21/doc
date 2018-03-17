---
title: 全文检索-Lucene
tags:
- Java
- Lucene
---
<!-- TOC -->

- [全文检索](#全文检索)
	- [检索](#检索)
- [Lucene](#lucene)
- [全文检索流程](#全文检索流程)
- [Lucene入门程序](#lucene入门程序)
- [索引:](#索引)
- [搜索:](#搜索)
- [搜索结果TopDocs](#搜索结果topdocs)
- [中文分词器](#中文分词器)

<!-- /TOC -->
# 全文检索

* 结构化数据：指具有固定格式或有限长度的数据,如数据库中的数据,元数据等.
* 非结构化数据：指不定长或无固定格式的数据,如邮件,word文档等.

## 检索

针对非结构化数据,有两种检索方式,
1. 顺序扫描法(Serial Scanning)
2. 全文检索(Full-text Search)

前者是对文件依次从前往后进行检索,可想而知效率极差.
后者是对文件先建立索引,然后依照索引进行检索,可大大提高检索效率,如字典的编纂.

实际的全文检索,首先把文本中的内容拆分成若干个关键词,然后根据关键词创建索引.此过程实际上是将关键词与包含该关键词的文本建立映射.
查询时,根据关键词查询索引,最终找到包含关键词的文本.整个过程类似于查字典的过程.

搜索引擎是全文检索技术最典型的应用
全文检索技术是搜索引擎的核心支撑技术

# Lucene

* 开源全文检索引擎工具包.
* 提供了完整的查询引擎和索引引擎，部分文本分析引擎.
* 在应用中实现全文检索的功能

使用Lucene建立起来的全文检索系统,用于提供全文检索服务,提供包括但不限于建立索引,处理查询返回结果集,增加索引,优化索引结构等功能

|package|功能|
|:---|:---|
|org.apache.lucene.analysis|语言分析器，分词器<br/>,Lucene提供的分析器实现类在：lucene-analyzers-common-4.10.3.jar|
|org.apache.lucene.document|索引存储时的文档结构管理，类似于关系型数据库的表结构|
|org.apache.lucene.index|索引管理，包括索引建立、删除等|
|org.apache.lucene.queryParser|查询分析器，实现查询关键词间的运算，如与、或、非等, 生成查询表达式，|
|org.apache.lucene.search|检索管理，根据查询条件，检索得到结果|
|org.apache.lucene.store|数据存储管理，包括一些I/O操作|
|org.apache.lucene.util|公用类|

# 全文检索流程

1. 数据采集
	数据采集技术:Solr
2. 创建索引
3. 定义查询语句
4. 搜索索引
5. 解析搜索的结果


# Lucene入门程序


mysql5.1驱动包：mysql-connector-java-5.1.7-bin.jar
lucene核心包：lucene-core-4.10.3.jar
lucene分析器通用包：lucene-analyzers-common-4.10.3.jar
lucene查询解析器包：lucene-queryparser-4.10.3.jar



3)	索引程序
		为什么要采集数据
		如何采集数据
		创建文档Document
		如何创建索引
		分词
		创建索引
4)	使用Luke查看索引
用于查询、修改lucene的索引文件
java  -jar lukeall-4.10.3.jar

5)	搜索程序
		输入查询语句
		搜索语法分析
		分词
		搜索索引

# 索引:
		Field域属性学习
		索引维护（添加,修改,删除）
# 搜索:
		使用Query子类对象搜索
		使用QueryParser进行搜索
# 搜索结果TopDocs
# 中文分词器
