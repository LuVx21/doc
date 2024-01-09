<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [Debezium](#debezium)
- [Maxwell](#maxwell)
- [canal](#canal)
    - [工作原理](#工作原理)
    - [canal HA](#canal-ha)
    - [canal_mysql_nosql_sync](#canal_mysql_nosql_sync)
- [阅读](#阅读)

<!-- /TOC -->
</details>

Change Data Capture

## Debezium

![architecture](https://debezium.io/images/debezium-architecture.png)

```bash
docker run -it --name zookeeper -p 2181:2181 -p 2888:2888 -p 3888:3888 debezium/zookeeper:1.2

docker run -it --name kafka -p 9092:9092 \
--link zookeeper:zookeeper debezium/kafka:1.2

docker run -it --name mysql -p 3306:3306 \
-e MYSQL_ROOT_PASSWORD=debezium \
-e MYSQL_USER=mysqluser \
-e MYSQL_PASSWORD=mysqlpw debezium/example-mysql:1.6

docker run -it --name mysqlterm \
--link mysql mysql:5.7 sh -c 'exec mysql -h"$MYSQL_PORT_3306_TCP_ADDR" -P"$MYSQL_PORT_3306_TCP_PORT" -uroot -p"$MYSQL_ENV_MYSQL_ROOT_PASSWORD"'

docker run -it --name connect -p 8083:8083 \
-e GROUP_ID=1 \
-e CONFIG_STORAGE_TOPIC=my_connect_configs \
-e OFFSET_STORAGE_TOPIC=my_connect_offsets \
-e STATUS_STORAGE_TOPIC=my_connect_statuses \
--link zookeeper:zookeeper \
--link kafka:kafka \
--link mysql:mysql \
debezium/connect:1.2
```

## Maxwell

支持应用内嵌入, 依赖为:

```xml
<dependency>
    <groupId>com.zendesk</groupId>
    <artifactId>maxwell</artifactId>
    <version>1.27.1</version>
</dependency>
```

[Github:maxwell](https://github.com/zendesk/maxwell)

其内部解析binlog使用了开源库:

```xml
<dependency>
    <groupId>com.zendesk</groupId>
    <artifactId>mysql-binlog-connector-java</artifactId>
    <version>0.23.2</version>
</dependency>
```

[Github:mysql-binlog-connector-java](https://github.com/osheroff/mysql-binlog-connector-java)

使用例:

```Java
public class App {
    public static final String USER = "root";
    public static final String PASSWORD = "root";
    public static final String HOST = "localhost";
    public static final int PORT = 3306;

    public static void main(String[] args) throws IOException {
        BinaryLogClient client = new BinaryLogClient(HOST, PORT, USER, PASSWORD);
        // EventDeserializer eventDeserializer = new EventDeserializer();
        // eventDeserializer.setCompatibilityMode(
        //         EventDeserializer.CompatibilityMode.DATE_AND_TIME_AS_LONG,
        //         EventDeserializer.CompatibilityMode.CHAR_AND_BINARY_AS_BYTE_ARRAY
        // );
        client.registerEventListener(event -> {
            System.out.println(event);
        });

        client.connect();
    }
}
```

## canal

产品定位:

基于 MySQL 数据库增量日志解析, 提供增量数据订阅和消费服务

应用场景:

最初开发被用于杭州和美国双机房同步需求 -> 数据增量同步

* 数据库镜像
* 数据库实时备份
* 索引构建和实时维护(拆分异构索引, 倒排索引等)
* 业务 cache 刷新
* 带业务逻辑的增量数据处理

安装配置阅读官网即可, 很详细的讲解

### 工作原理

1. canal自身模拟主从的slave角色, 使用主从通信的交互协议, 向master发送获取binlog请求
2. canal获取master过来的binlog
3. canal解析binlog(基于字节流)

内部工作流程:

1. 获取上次解析位置 -> 读取记录的读取位置(日志的定位靠`文件名+position`), 不存在则读取`show binary logs`的起始
2. 建立主从连接, 请求binlog
3. 拉取binlog
4. 解析器解析binlog
5. 后续日志处理 -> 读取日志是读取全部的, 不能做到选择获取或者筛选获取, 这样的操作交给`EventSink`
6. 记录本次读取位置(client消费时, 会有`ack`,`rollback`等位置的操作)

在`Binlog Parser`+`Event Sink`中做的工作大致如下:

首先把binlog文件, 解析成多个event, 放入到相关队列中, 实际上, canal借鉴了Disruptor(高性能队列)的思想, 定义了3个cursor:

* Put: Sink模块进行数据存储的最后一次写入位置
* Get: 数据订阅获取的最后一次提取位置
* Ack: 数据消费成功的最后一次消费位置

在实现上, 为了尽可能加快解析速度, 可以让用户指定解析的开始位置或者起始消费的时间.

把binlog文件解析成binlog event后, 再判断下是否符合指定的筛选条件, 如满足特定规则的表, 若不符合, 则丢弃该event.

![22](https://awps-assets.meituan.net/mit-x/blog-images-bundle-2017/044be049.png)

> 扩展: 数据库回滚实现

### canal HA

ZooKeeper的`数据发布/订阅`典型应用场景

![33](https://camo.githubusercontent.com/c8f1d98268a307821273e94e7eefcd29a26f9b78/687474703a2f2f646c2e69746579652e636f6d2f75706c6f61642f6174746163686d656e742f303038302f333330332f64333230326332362d653935342d333563302d613331392d3537363034313032633537642e6a7067)

大致步骤:

1. canal server要启动某个canal instance时都先向zookeeper进行一次尝试启动判断 (实现:  创建`EPHEMERAL`节点, 谁创建成功就允许谁启动)
2. 创建zookeeper节点成功后, 对应的canal server就启动对应的canal instance, 没有创建成功的canal instance就会处于standby状态
3. 一旦zookeeper发现canal server A创建的节点消失后, 立即通知其他的canal server再次进行步骤1的操作, 重新选出一个canal server启动instance.
4. canal client每次进行connect时, 会首先向zookeeper询问当前是谁启动了canal instance, 然后和其建立链接, 一旦链接不可用, 会重新尝试connect.

![image-20191121161039727](https://gitee.com/LuVx/img/raw/master/mysql/image-20191121161039727.png)

![image-20191121161313617](https://gitee.com/LuVx/img/raw/master/mysql/image-20191121161313617.png)

![image-20191121161652510](https://gitee.com/LuVx/img/raw/master/mysql/image-20191121161652510.png)

![image-20191121161856840](https://gitee.com/LuVx/img/raw/master/mysql/image-20191121161856840.png)

故障转移:

![image-20191122102256575](https://gitee.com/LuVx/img/raw/master/mysql/image-20191122102256575.png)

### canal_mysql_nosql_sync

基于canal 的 mysql 与 redis/memcached/mongodb 的 nosql 数据实时同步方案

[Github:canal_mysql_nosql_sync](https://github.com/liukelin/canal_mysql_nosql_sync)

## 阅读

0. [Canal Kafka RocketMQ QuickStart](https://github.com/alibaba/canal/wiki/Canal-Kafka-RocketMQ-QuickStart)
