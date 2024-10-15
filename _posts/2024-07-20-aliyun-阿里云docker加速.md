---
title: "阿里云docker加速"
subtitle: ""
layout: post
author: "Tidcl"
header-style: text
hidden: false
plchart: true
tags:
  - docker
  - 阿里云
---



阿里云docker拉取镜像提示：error pulling image configuration: download failed after attempts=6: dial tcp 47.88.58.234:443: connect: connection refused

解决办法：获取阿里云专属加速服务
1、登录阿里云
2、点击右上方控制台
3、点击左上弹出菜单，选择“产品与服务”-“容器”，点击“容器镜像服务器ACK”
4、点击“容器镜像服务”-“镜像工具”-“镜像加速器”
5、根据操作文档操作即可加速docker


其他办法：
可以通过配置/etc/docker/daemon.json指定其他国内源