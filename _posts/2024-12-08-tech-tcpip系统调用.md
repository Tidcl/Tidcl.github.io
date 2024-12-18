---
title: "posix api之tcpip协议"
subtitle: " "
layout: post
author: "Tidcl"
header-style: text
hidden: false
plchart: true
tags:
  - tcp/ip

---



## posix api

posix api接口是一套由IEEE（电气和电子工程师协会）定义的操作系统接口标准，包括多个方面，如进程管理、线程管理、文件管理、网络通信等，这篇文章着重介绍一下网络通信的posix api接口，如socket、bind、listen、accept、close、connect函数



## tcp/ip 协议

tcp/ip是一组用于数据通信的网络协议，包括了多个层次的不同协议：链路层（ether、wlan）、网络层（arp、icmp、ip）、传输层（tcp、udp）、应用层（http、https、ftp、smtp、dns、ssh、dhcp）

tcp/ip协议使用非常广泛，互联网绝大多数传输都是使用tcp/ip协议。



## tcp协议

### tcp状态机的状态

一共有11种状态：closed、listen、syn_rcvd、syn_sent、established、fin_wait_1、fin_wait_2、time_wait、close_wait、last_ack、closing。
每种状态在满足条件后进入下一种状态，下面提到的三次握手四次挥手都是在状态间切换。

### 三次握手四次挥手

tcp有三次握手和四次挥手。
三次握手流程：

1. 客户端发送syn seq1 进入syn_sent
2. 服务端回复ack ack1  syn seq1，从listen进入syn_rcvd
3. 客户端回复ack ack1，客户端从syn_sent进入established，服务端从syn_rcvd进入established

四次挥手流程其中一种：

主动close的一方

1. 主动方close()调用，发送fin 进入fin_wait_1
2. 主动方收到ack 进入fin_wait_2
3. 主动方收到fin 从fin_wait_2进入time_wait

接收到recv=0 被动关闭一方

1. 收到fin 回复ack 进入close_wait 
2. 调用close()发送fin 进入last_ack

![image-20241210104113754](http://Tidcl.github.io/img/posts/2024-12-08-tech-tcpip.assets/image-20241210104113754.png)

### 可靠性机制

tcp协议通过序列号、确认应答、超时重传机制实现可靠性。

### 流量控制，网络拥塞控制

tcp协议通过滑动窗口机制来控制发送速率，避免因发送过多导致接收方处理不过来。
当tcp协议传输数据包发生丢失导致收到重复ack，或一直收不到ack出现超时重传，会判断是否发生拥塞，如果是会降低发送速率，主动降低网络的负载。

### tcb与tcp

首先tcb不是新的协议，它的全称是 Transmission Control Block 传输控制块，是网络传输使用到的的数据结构，tcp和udp在传输时都会使用到tcb。tcb用于维护tcp状态和处理tcp数据，每个tcp连接都有一个对应tcb，tcb存储tcp连接的数据，包括seq num、ack num等。

socket函数，创建一个socket时，就会在内核创建一个tcb，该tcb没有任何连接信息。
bind函数，设置tcb的ip和port。
listen函数，设置tdb，使socket进入监听状态。
accept函数，从全连接队列中取出一个tcb，并分配一个fd给tab，返回客户端fd。
connect函数，通过fd找到tcb，设置ip和port，发送syn包建立连接，进入syn_sent。
close函数，通过fd找到tcb，发送fin包断开连接进入fin_wait_1。