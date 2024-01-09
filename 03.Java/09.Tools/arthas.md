<details>
<summary>点击展开目录</summary>
<!-- TOC -->

- [Arthas](#arthas)
- [命令](#命令)
- [场景](#场景)
- [方法调用链耗时](#方法调用链耗时)
- [查看方法入出参](#查看方法入出参)
- [执行静态方法](#执行静态方法)
- [获取静态变量](#获取静态变量)
- [运行时替换代码](#运行时替换代码)
- [JVM相关](#jvm相关)

<!-- /TOC -->
</details>

## Arthas

总结一下[Arthas](https://alibaba.github.io/arthas/)的使用场景及例子


java.lang.instrument.Instrumentation
redefineClasses
retransformClasses

BTrace
Arthas


## 命令


## 场景

## 方法调用链耗时

```Bah
trace org.luvx.post.service.impl.LiteMomentOperationServiceRpcClient createMoment
```

## 查看方法入出参
```
watch org.luvx.common.util.FeedIteratorRendViewUtil rendFeedView "{params,returnObj}" -s -x 1
```

## 执行静态方法

1.


```bash
# 对象 id
sc -d -f org.luvx.ddp.common.utils.ApplicationContextUtils
// 可行
ognl '@org.luvx.ddp.common.utils.ApplicationContextUtils@applicationContext.getBean("dashboardTask")' -c 3af49f1c
// 可行 -> 实际效果有点不对
ognl '@org.luvx.ddp.common.utils.ApplicationContextUtils@applicationContext.getBean("dashboardTask").init()' -c 3af49f1c
```


## 获取静态变量

`getstatic org.luvx.tdata.dwhtools.task.GetBinlogTask activeThreadSet`
`ognl '@org.luvx.tdata.dwhtools.task.GetBinlogTask@activeThreadSet' -c 3af49f1c`


## 运行时替换代码



## JVM相关



