<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [OLTP/OLAP](#oltpolap)
- [Apache Kylin](#apache-kylin)
- [Mondrian](#mondrian)
- [Saiku](#saiku)
- [整合](#整合)
- [参考](#参考)

<!-- /TOC -->
</details>

## OLTP/OLAP

联机事务处理OLTP(on-line transaction processing)
实时性要求高
数据量不是很大
高并发, 并且要求满足ACID原则

联机分析处理OLAP(On-Line Analytical Processing)

## Apache Kylin

[官网](http://kylin.apache.org/cn/)

Apache Kylin™是一个开源的分布式分析引擎, 提供Hadoop/Spark之上的SQL查询接口及多维分析(OLAP)能力以支持超大规模数据, 
最初由eBay Inc. 开发并贡献至开源社区. 它能在亚秒内查询巨大的Hive表.

类似的有Presto, [Druid](https://druid.apache.org)

## Mondrian

[官网](https://github.com/pentaho/mondrian)

[资料](http://wiki.smartbi.com.cn:18081/pages/viewpage.action?pageId=3866724)

## Saiku

[官网](https://community.meteorite.bi)

最先版本中已集成Mondrian

类似的有caravel

## 整合

https://github.com/mustangore/kylin-mondrian-interaction


## 参考

[Kylin, Mondrian, Saiku系统的整合](https://tech.youzan.com/kylin-mondrian-saiku/)
http://lxw1234.com/archives/2016/05/647.htm



