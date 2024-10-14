```
title: "windows编译ffmpeg库"
subtitle: ""
layout: post
author: "Tidcl"
header-style: text
hidden: false
plchart: true
tags:
  - ffmpeg
  - msys2
  - mingw
```



使用msys2，并且在msys2中安装pacman -S mingw-w64-x86_64-toolchain ，添加mingw/bin路径到环境变量
### 编译ffmpeg:

打开 MSYS2 CLANG64，并cd切换到ffmpeg目录

```shell
./configure --prefix=${basepath}/x264_install --enable-shared
make -j8
make install #会安装到./configure中指定的prefix参数目录中
```

## 使用vscode编译自己写的ffmpeg测试程序：
vdcode打开目录，配置生成器，按 ctrl+shift+p 输入 open user settings

设置settings.txt中配置配置"cmake.generator": "MinGW Makefiles" ，如果没有cmake.generator选项需要自己添加（没有安装cmake插件需要安装），当cmake通过CMakeLists.txt构建出Makefile项目后，需要使用mingw32-make编译构建出的Makefile项目。

### 配置编译器路径

把mingw32-make.exe的目录配置到环境变量path中（通过msys2安装就去msys2的目录中找到mingw，比如 C:\msys64\mingw64\bin ）。



编写CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)
include_directories("D:/ffmpeg-7.0.1/build/include") 替换自己的目录
link_directories("D:/ffmpeg-7.0.1/build/bin") 替换自己的目录
add_executable(test avio_read_callback.c)
target_link_libraries(test avcodec avformat avutil)
```

在VSCode文件列表中，选中CMakeLists.txt，并右键菜单生成所有项目

