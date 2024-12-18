---
title: "numa是什么？"
subtitle: " "
layout: post
author: "Tidcl"
header-style: text
hidden: false
plchart: true
tags:
  - numa

---



## numa是什么？

NUMA（Non-Uniform Memory Access）非一致性内存访问，是多CPU如何访问内存的架构模型。在早期的cpu-内存架构中，核心数量少，核心和内存使用总线连接，所有的核心都是通过一条总线来访问内存，这种架构叫做SMP架构（Symmetric Multi-Processor）对称多处理器结构。



## 为什么提出numa？

因为smp架构核心使用同一总线，核心数量多起来，为了保证缓存一致性，总线成为了无法发挥核心的瓶颈。
而numa通过将核心划分到不同numa node中，每个numa node都有独立的内存空间和pcie总线。带来的效果就是，如果核心访问numa node中的本地内存，速度最快；访问其他numa node中的内存，访问速度与距离有关。



## 开发程序时注意事项

numa架构把numa node和内存绑定在一起，并且把核心分散在不同numa node中，带来的结果就是核心的访问速度提升，访问远距离numa node内存的速度跟距离相关。

在程序开发时，如果是numa架构，就更应该考虑局部性原理，**程序在短期内倾向于重复访问相同的数据** **程序在访问内存时倾向于访问相邻的数据**。



## 使代码满足局部性原理

按顺序访问内存，避免跳跃访问，以提高空间局部性。
重用对象而不是频繁地创建和销毁，以利用时间局部性。

提前加载可能需要的数据到缓存中，以利用时间局部性。
减少循环迭代次数，减少循环控制开销，同时增加每次迭代中执行的指令数，提高空间局部性。
将线程绑定到特定的CPU核心，以减少上下文切换和提高缓存利用率。



## numa指令介绍

lscpu 查看NUMA和CPU的对应关系。

numactl -H 命令可以看到NUMA下的内存分布。

numastat 命令查看NUMA系统下内存的访问命中率。