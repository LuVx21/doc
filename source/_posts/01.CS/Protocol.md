---
title: 计算机网络体系结构
date: 2016-04-16
tags:
- CS
---
<!-- TOC -->

- [](#)
- [OSI七层结构](#osi七层结构)
- [TCP/IP四层结构](#tcpip四层结构)
- [参考](#参考)

<!-- /TOC -->


# 

协议定义了 网络实体间发送报文和接收报文的格式、顺序以及当传送和接收消息时应采取的行动(规则)。

协议数据单元(PDU)Protocol Data Unit,由 协议控制信息(协议头) 和 数据(SDU) 组成

协议头部中含有完成数据传输所需的控制信息，比如地址、序号、长度、分段标志、差错控制信息等。传输层及以下各层的PDU均有各自特定的名称：

传输层: 段(Segment)
网络层: 分组/包(Packet)
数据链路层: 帧(Frame)
物理层: 比特(Bit)

数据在源站自上而下递交的过程实际上就是不断封装的过程，而到达目的地后自下而上递交的过程就是不断拆封的过程

![](https://raw.githubusercontent.com/LuVx21/hexo/master/source/_posts/01.CS/img/PDU封装实例.png)

IP:不可靠协议,不会重复,使用ICMP协议通知失败
ICMP:

SNMP:简单网络管理协议

# OSI七层结构

开放系统互连基本参考模型OSI/RM (Open System Interconnection Reference Model)

![](https://raw.githubusercontent.com/LuVx21/hexo/master/source/_posts/01.CS/img/典型网络体系结构.png)


# TCP/IP四层结构


# 参考

[计算机网络体系结构综述（下）](https://blog.csdn.net/justloveyou_/article/details/69612153)