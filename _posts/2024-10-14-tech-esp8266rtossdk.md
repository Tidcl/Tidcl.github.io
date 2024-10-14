﻿---
title: "Win环境下ESP8266 RTOS SDK编译烧录"
subtitle: " "
layout: post
author: "Tidcl"
header-style: text
hidden: false
plchart: true
tags:
  - ESP8266
  - RTOS
---



# 在Windows环境将ESP8266 RTOS SDK编译烧录进ESP8266MOD(ESP-12E)开发板



## 获取资源 [Standard Setup of Toolchain for Windows — ESP8266 RTOS SDK Programming Guide documentation (espressif.com)](https://docs.espressif.com/projects/esp8266-rtos-sdk/en/latest/get-started/windows-setup.html)

1. 编译环境：https://dl.espressif.com/dl/esp32_win32_msys2_environment_and_toolchain-20181001.zip
   在windows模拟linux环境，提供一系列工具包集合，像在linux环境下编译代码，通过pacman管理工具包。
2. 工具链（用于SDK高于3.0版本）：https://dl.espressif.com/dl/xtensa-lx106-elf-gcc8_4_0-esp-2020r3-win32.zip
   编译代码时所需的工具集合。
3. ESP8266 RTOS SDK代码（2024年10月14日SDK版本为v3.4）：https://github.com/espressif/ESP8266_RTOS_SDK/releases/tag/v3.4
   ESP8266 RTOS Software Development Kit (SDK)是ESP8266 RTOS的软件开发套件，提供了在开发ESP8266 RTOS软件时用到的头文件，三方库，工具等资源。

## 搭建环境

解压esp32_win32_msys2_environment_and_toolchain-20181001.zip到msys32目录中。

解压xtensa-lx106-elf-gcc8_4_0-esp-2020r3-win32.zip到msys32/opt目录，并打开msys32/etc/profile.d/esp32_toolchain.sh配置，如果不配置会编译错误。

```shell
export PATH="$PATH:/opt/xtensa-lx106-elf/bin"
```

打开msys32/etc/profile.d/esp32_toolchain.sh配置环境变量IDF_PATH，该变量指向ESP8266_RTOS_SDK目录

```shell
export IDF_PATH="xxx/ESP8266_RTOS_SDK" xxx替换为自己电脑上路径
```

以上环境变量也可以打开在控制台中配置，写在配置文件为了永久生效。

安装所需的Python包（报错请查看下方）：

```shell
python2.7 -m pip install --user -r $IDF_PATH/requirements.txt
```

打开，msys32/mingw32.exe

```shell
cd $IDF_PATH\examples\get-started\hello_world
make menuconfig
```

选择“Serial flasher config --->”

 ![菜单1](https://tidcl.github.io/img/posts/esp8266rtossdk/菜单1.PNG)

配置"Default serrial port"为开发板端口，我的是COM3

![端口](https://tidcl.github.io/img/posts/esp8266rtossdk/端口.PNG)

配置"Flash size"，我的开发板是4M

![菜单2](https://tidcl.github.io/img/posts/esp8266rtossdk/菜单2.PNG)

make all							 编译

make flash					     烧录

![烧录成功](https://tidcl.github.io/img/posts/esp8266rtossdk/烧录成功.PNG)



使用串口调试助手设置74880波特率、校验位NONE、数据位8、停止位1，打开串口接收消息。

## 出现的问题

### 报错 **make：xtensa-lx106-elf-gcc**，需要将工具链**xtensa-lx106-elf/bin**目录添加进环境变量

```
export PATH="$PATH:/opt/xtensa-lx106-elf/bin"
```

### python -m pip install --user -r $IDF_PATH/requirements.txt 报错：

```shell
 No matching distribution found for setuptools>=46.4.0

  ----------------------------------------
Command "E:/Code/esp8266rtos/msys32/mingw32/bin/python.exe -m pip install --ignore-installed --no-user --prefix c:/users/admini~1/appdata/local/temp/pip-build-env-e5zjhi --no-warn-script-location --no-binary :none: --only-binary :none: -i https://pypi.org/simple -- "setuptools >= 46.4.0"" failed with error code 1 in None
```

因为requirements.txt文件中setuptools没有指定版本，但是下载的环境包中已经安装了setuptools 40.4.3，python2应该不能安装setuptools 46.4.0（还未验证）导致一直无法安装最新的46.4.0，将requirements.txt中的setuptools改为setuptools>=40.4.3即可。

requirements.txt中只是为了安装python包，换个思路只需要安装好对应的python包即可，当出现下面的错误时，需要手动安装相关的python包。

### make menuconfig 报错(因为python包没有正确安装，本质还是requirements.txt问题)：

```shell
The following Python requirements are not satisfied:
click>=5.0
pyelftools>=0.22
The recommended way to install a packages is via "pacman". Please run "pacman -Ss <package_name>" for searching the package database and if found then "pacman -S mingw-w64-i686-python2-<package_name>" for installing it.
NOTE: You may need to run "pacman -Syu" if your package database is older and run twice if the previous run updated "pacman" itself.
Please read https://github.com/msys2/msys2/wiki/Using-packages for further information about using "pacman"
Alternatively, you can run "E:/Code/esp8266rtos/msys32/mingw32/bin/python.exe -m pip install --user -r E:/Code/esp8266rtos/ESP8266_RTOS_SDK/requirements.txt" for resolving the issue.
make: *** 没有规则可制作目标“check_python_dependencies”，由“menuconfig” 需求。 停止。
```

提示没有click和pyelftools包，解决办法手动安装：

```shell
python2.exe -m pip install click
python2.exe -m pip install pyelftools==0.22
```


这样运行python -m pip install --user -r $IDF_PATH/requirements.txt就不会报错了，运行make menuconfig也会正常了。



make menuconfig		    配置rtos系统

make clean						清除

make all							 编译

make flash					     烧录

make monitor				  查看串口输出




参考文章：https://www.cnblogs.com/dongxiaodong/p/12905967.html