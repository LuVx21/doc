---
title: 消息队列:RabbitMQ
date: 
tags:
- RabbitMQ
---
<!-- TOC -->

- [关于](#关于)
    - [组件](#组件)
- [基本使用](#基本使用)
- [API](#api)
    - [Exchange](#exchange)
    - [Binding](#binding)
- [配置命名规范](#配置命名规范)
- [参考](#参考)

<!-- /TOC -->

# 关于

MQ全称为Message Queue,RabbitMQ基于AMQP(Advanced Message Queue)协议实现

在项目中, 将一些无需即时返回且耗时的操作提取出来, 进行了异步处理, 而这种异步处理的方式大大的节省了服务器的请求响应时间, 从而提高了系统的吞吐量

## 组件

Brocker: 消息队列服务器实体.
Exchange: 消息交换机, 指定消息按什么规则, 路由到哪个队列.
Queue: 消息队列, 每个消息都会被投入到一个或者多个队列里.
Binding: 绑定, 它的作用是把exchange和queue按照路由规则binding起来.
Routing Key: 路由关键字, exchange根据这个关键字进行消息投递.
Vhost: 虚拟主机, 一个broker里可以开设多个vhost, 用作不用用户的权限分离.
Producer: 消息生产者, 就是投递消息的程序.
Consumer: 消息消费者, 就是接受消息的程序.
Channel: 消息通道, 在客户端的每个连接里, 可建立多个channel, 每个channel代表一个会话任务

# 基本使用

```shell
# 使用管理工具,端口15672
rabbitmq-plugins enable rabbitmq_management
# 启动
# rabbitmq-server start
rabbitmq-server -detached
# 关闭
rabbitmqctl stop
rabbitmqctl add_user luvx 1121
```


# API

ConnectionFactory, Connection, Channel

定义Queue, 定义Exchange, 绑定Queue与Exchange, 发布消息

生产者 -> Exchange -> Queue -> 消费者

## Exchange

Exchange Types:
* fanout:路由到所有与它绑定的Queue
* direct:把消息路由到那些binding key与routing key完全匹配的Queue
* topic:在direct的基础上支持模糊匹配,binding key和routing key由多个单词组成,以`.`分割,使用`*`与`#`分别匹配一个和多个单词
* headers:根据发送的消息内容中的headers属性内容进行匹配

## Binding

将Exchange与Queue关联


# 配置命名规范

```conf
#命名规范:容器名称.[队列特点or路由特点].使用的平台名称.作用
#@容器名称:queue,exchange
#@队列特点:非持久化标记(undurable),延时队列(delay),优先级队列(priority)
#@路由特点:direct,topic,fanout,headers
#@使用的平台名称:luvx
#@作用:干什么的
#eg:
# 消息队列(queue.luvx.message)
# 延时消息队列(queue.delay.luvx.message)
# 普通路由(exchange.direct.luvx.common)
# 通用路由(exchange.direct.luvx.common)
rmq.queue.luvx.test=queue.luvx.test
rmq.queue.undurable.luvx.test=queue.undurable.luvx.test
rmq.queue.delay.luvx.test=queue.delay.luvx.test
rmq.queue.priority.luvx.test=queue.priority.luvx.test
rmq.exchange.direct.luvx.test=exchange.direct.luvx.test
rmq.exchange.fanout.luvx.test=exchange.fanout.luvx.test
rmq.exchange.headers.luvx.test=exchange.headers.luvx.test
```

# 参考

[1](https://blog.csdn.net/lyhkmm/article/details/78775369)
https://www.jianshu.com/p/a6460b4b155f
http://wiki.jikexueyuan.com/project/rabbitmq/
https://zhuanlan.zhihu.com/p/24335916
https://blog.fundebug.com/2018/04/20/rabbitmq_tutorial/