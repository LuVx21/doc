---
title: 网络
date: 2017-12-03
tags:
- Java
---

<!-- TOC -->

- [TCP 与 UDP](#tcp-与-udp)

<!-- /TOC -->

![alt](./img/protocol.jpg)
# TCP 与 UDP

网络层: IP协议、ICMP协议、ARP协议、RARP协议和BOOTP协议
传输层: TCP协议与UDP协议
应用层: FTP、HTTP、TELNET、SMTP、DNS等协议
HTTP是应用层协议，其传输都是被包装成TCP协议传输。可以用SOCKET实现HTTP。

TCP:传输控制协议,提供的是面向连接、可靠的字节流服务.提供超时重发，丢弃重复数据，检验数据，流量控制等功能，保证数据能从一端传到另一端
UDP:用户数据报协议，是一个无连接的不可靠的面向数据报协议

三次握手:
'三次握手，四次拜拜'

第一次握手：客户端发送syn包(syn=j)到服务器，并进入SYN_SEND状态，等待服务器确认；
第二次握手：服务器收到syn包，必须确认客户的SYN（ack=j+1），同时自己也发送一个SYN包（syn=k），即SYN+ACK包，此时服务器进入SYN_RECV状态；
第三次握手：客户端收到服务器的SYN＋ACK包，向服务器发送确认包ACK(ack=k+1)，此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手。
