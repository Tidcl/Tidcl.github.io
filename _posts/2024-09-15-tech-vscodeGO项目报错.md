---
title: "vscode golang项目报错，环境配置错误"
subtitle: ""
layout: post
author: "Tidcl"
header-style: text
hidden: false
plchart: true
tags:
  - golang
  - vscode
---





### 在vscode控制台（ctrl+shift+p打开控制台）输入go install

报错：command 'go.tools.install' not found
即使已经配置环境变量安装go扩展插件仍然报错（环境变量配置在文章末尾）

调式代码，提示下载dlv报错：dlv: failed to install dlv(github.com/go-delve/delve/cmd/dlv@latest): Error: Command failed: C:\Program Files\Go\bin\go.exe install -v github.com/go-delve/delve/cmd/dlv@latest

### 解决办法：

**管理员权限**启动vscode，再打开golang项目

## 配置go环境变量：
golang默认安装路径
GOPATH：C:\Program Files\Go\bin
GOROOT：C:\Program Files\Go
PATH添加C:\Program Files\Go\bin