---
title: "reactor的本质"
subtitle: " "
layout: post
author: "Tidcl"
header-style: text
hidden: false
plchart: true
tags:
  - epoll
  - reactor
---



# Reactor

### reactor是什么？

Reactor也称为反应器模型，是一种将就绪事件派发给对应处理程序的网络模型。它采用事件驱动架构，将I/O的各种状态变化封装成事件，并使用I/O多路复用技术，实现单个线程处理多个客户端的I/O事件。

### 为什么使用reactor？

reactor模型通过将io进行封装，实现了单线程多连接处理，提高了并发量；且事件驱动机制使得io处理更加容易高效，减少了多线程带来的竞态环境；reactor本身也很容易复用，因此也出现了很多reactor变体。reactor高并发本质是因为io多路复用，reactor网络模型带来的更多好处是reactor修改了io处理的方式，通过事件注册回调的方式处理io。

## reactor模型的基础

### reactor实现细节

因为reactor是依赖io多路复用实现的，那么要实现一个reactor需要做什么操作？

选择一个io多路复用技术，例如epoll。
设计reactor的数据结构和函数，要实现reactor对fd管理，如fd的读写缓冲和回调函数。

```c++
typedef struct connect_s { //这是一个连接，reactor通过该结构对一个fd进行管理
    int fd; 		//文件io fd
    char rbuffer[BUFFER_LENGTH]; 	//读缓冲
    int rc;							//读缓冲字符数量
    char wbuffer[BUFFER_LENGTH];
    int wc;  
    callback cb;					//事件回调函数
}connect_t;
```



## reactor模型变体

### 单reactor单线程处理

最简单的reactor使用，通过单线程启用一个reactor，进行连接处理和数据处理，可能出现业务处理较慢。

### 单reactor多线程处理

连接处理与上面单reactor单线程处理模型一样，但是不同的是数据处理通过多线程处理，处理之后再交给reactor发送。这样业务和io处理就分开了。

### 主从reactor多线程

主reactor负责listenfd处理连接，从reactor负责管理clientfd负责处理。 Netty 和 Memcache 都采用了该方案。

## reactor优势与局限

### reactor模型 vs proactor模型

**事件检测和处理方式**：
在事件发生时就通知事先注册的事件处理函数，读写操作需要应用程序同步操作，是非阻塞同步网络模型。
基于异步I/O完成读写操作（由内核完成），待I/O操作完成后才回调应用程序的处理器来进行业务处理，是异步网络模型。

**性能**：
理论上Proactor模型比Reactor模型效率更高，因为异步I/O更加充分发挥DMA的优势。
由于异步操作流程的事件的初始化和事件完成在时间和空间上都是相互分离的，因此开发异步应用程序更加复杂。

**编程复杂性**：
模型简单，没有多线程、进程通信、竞争的问题。
编程复杂性高，由于异步操作流程的事件的初始化和事件完成在时间和空间上都是相互分离的。

**内存使用和操作系统支持**：
在Socket已经准备好读或写前，不要求开辟缓存。
每个并发操作都要求有独立的缓存，可能造成持续的不确定性，并且操作系统支持方面，Windows通过IOCP实现了真正的异步I/O，而在Linux系统下，异步I/O还不完善。

Reactor模型适用于需要高效处理大量并发连接的场景，能够简化多线程管理并减少上下文切换的开销。而Proactor模型通过完全异步的方式提高了并发处理的能力，特别是在I/O密集型的应用中展现出更高的性能。选择哪种模型取决于具体的应用场景和需求。



## 总结

reactor是一种网络模型，通过将io多路复用进行封装，提供更方便操作的接口，有三种常用的使用方式。
应用程序使用reactor更像是让reactor作为一个“通知”角色，而proactor更像是作为一个“执行”角色，比起proactor使用reactor还需要在事件触发时进行额外的io操作，额外的io操作又容易引起拷贝和上下文切换带来的损失。