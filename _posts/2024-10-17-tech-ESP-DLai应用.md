---
title: "Vscode使用ESPIDF插件开发esp32s3程序"
subtitle: " "
layout: post
author: "Tidcl"
header-style: text
hidden: false
plchart: true
tags:
  - ESP32S3
  - vscode
  - espidf

---





VSCode v1.94.2

ESP-IDF v5.3.1

ESP32-S3-WROOM-1

网络有使用vpn



### 官方文档

[快速入门 - ESP32-S3 - — ESP-IDF 编程指南 v5.3.1 文档 (espressif.com)](https://docs.espressif.com/projects/esp-idf/zh_CN/v5.3.1/esp32s3/get-started/index.html)

### API文档

[API 参考 - ESP32-S3 - — ESP-IDF 编程指南 v5.3.1 文档 (espressif.com)](https://docs.espressif.com/projects/esp-idf/zh_CN/v5.3.1/esp32s3/api-reference/index.html)



## vscode集成ESP-IDP开发环境

官方教程[vscode-esp-idf-extension/docs/tutorial/install.md 在 master ·espressif/vscode-esp-idf-扩展 (github.com)](https://github.com/espressif/vscode-esp-idf-extension/blob/master/docs/tutorial/install.md)

整体还是顺利，一把过。

## Hello world example

安装完成后，首先跑一个hello world示例程序。

按ctrl+shitf+p打开命令面板，输入Configure ESP-IDF Extension，调出ESP-IDF扩展欢迎界面

![image-20241017155852287](https://tidcl.github.io/img/posts/ESP-DLai应用.assets/image-20241017155852287.png)

![image-20241017151801771](https://tidcl.github.io/img/posts/ESP-DLai应用.assets/image-20241017151801771.png)

选择“Show example”，找到“get-started”-“hello_world”，点击“Create project using example hello_world”。项目文件夹创建的目录，注意创建的是项目文件夹而不是一堆散乱的项目文件，不需要自己再建一个空文件夹存放。

![image-20241017152302720](https://tidcl.github.io/img/posts/ESP-DLai应用.assets/image-20241017152302720.png)



## ESP-IDF项目环境配置

使用vscode打开hello world程序的文件夹。首先在vsvode底部状态栏有一排按钮，是属于ESP-IDF的拓展，每个按钮功能都不一样

![image-20241017151926239](https://tidcl.github.io/img/posts/ESP-DLai应用.assets/image-20241017151926239.png)

对于一个新项目来说，操作流程图下

1. 选择ESP-IDF套件![image-20241017164513618](https://tidcl.github.io/img/posts/ESP-DLai应用.assets\image-20241017164513618.png)

2. 选择端口![image-20241017154152408](https://tidcl.github.io/img/posts/ESP-DLai应用.assets\image-20241017154152408.png)

3. 选择Espressif目标设备![image-20241017155616949](https://tidcl.github.io/img/posts/ESP-DLai应用.assets\image-20241017155616949.png)

4. 选职烧录方式![image-20241017154217483](https://tidcl.github.io/img/posts/ESP-DLai应用.assets\image-20241017154217483.png)

5. 构建项目![image-20241017154209591](https://tidcl.github.io/img/posts/ESP-DLai应用.assets\image-20241017154209591.png)，构建完成：

![image-20241017160101109](https://tidcl.github.io/img/posts/ESP-DLai应用.assets\image-20241017160101109.png)

6. 烧录设备![image-20241017154226167](https://tidcl.github.io/img/posts/ESP-DLai应用.assets\image-20241017154226167.png)，烧录成功：

![image-20241017160241108](https://tidcl.github.io/img/posts/ESP-DLai应用.assets\image-20241017160241108.png)

7. 监控设备![image-20241017154235022](https://tidcl.github.io/img/posts/ESP-DLai应用.assets\image-20241017154235022.png)

每个按钮图表将鼠标移上去都有解释。



## Vscode问题

通过![image-20241017162852999](https://tidcl.github.io/img/posts/ESP-DLai应用.assets\image-20241017162852999.png)打开ESP-IDF环境后，输入idf.py menuconfig进行配置，发现上下左右键不起作用，这是vscode的问题，上下左右分别映射成了kjhl：

↑ - K
↓ - J
← - H
→ - L