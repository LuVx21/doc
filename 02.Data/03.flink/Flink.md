<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [教程](#教程)
- [配置](#配置)
- [Data Sources](#data-sources)

<!-- /TOC -->
</details>


## 教程

[Apache Flink 钉钉群直播教程-基础篇](https://ververica.cn/developers/flink-training-course1/)

[Apache Flink 钉钉群直播教程-进阶篇](https://ververica.cn/developers/flink-training-course2/)

[Apache Flink 钉钉群直播教程-运维篇&实战篇](https://ververica.cn/developers/flink-training-course3/)

[Apache Flink 钉钉群直播教程-源码篇](https://ververica.cn/developers/flink-training-course4/)


[例子代码sql-training](https://github.com/ververica/sql-training)

## 配置

web ui地址;

`http://localhost:8081`

定义上传jar包路径(默认路径下, 服务重启自动删除):

```yml
web.upload.dir: /opt/flink/target/
```

## Data Sources

实现 `SourceFunction` 自定义非并行的 source
实现 `ParallelSourceFunction` 接口或者扩展 `RichParallelSourceFunction` 自定义并行的 source

![](https://gitee.com/LuVx/img/raw/master/flink_class_Function.png)



