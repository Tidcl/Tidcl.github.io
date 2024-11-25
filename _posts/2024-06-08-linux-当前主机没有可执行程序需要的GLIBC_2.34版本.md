---
title: "GLIBC找不到"
subtitle: ""
layout: post
author: "Tidcl"
header-style: text
hidden: false
plchart: true
tags:
  - linux
  - GLIBC
---



执行程序报错提示/lib/x86_64-linux-gnu/libc.so.6: version `GLIBC_2.34' not found 
我这台电脑上的libc只是2.30版本，没有到2.34，因为ubuntu20.04最高就是2.30


不要去尝试添加ubutnu22.04等更高的版本源，直接更新GLIBC可能导致系统崩溃，推荐使用docker拉取ubuntu镜像，并把程序复制进去运行
如果有程序源码，直接把程序在本地编译一遍即可，这样程序就可以使用已有的glibc2.30版本，即使没有glibc2.34也没关系。