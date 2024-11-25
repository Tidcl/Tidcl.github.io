---
title: "ESP32S3运行micro-os"
subtitle: " "
layout: post
author: "Tidcl"
header-style: text
hidden: false
plchart: true
tags:
  - ESP32S3
  - micro-os
  - platformio
  - espidf
---





前段时间使用esp-idf体验了一下rtos，多线程使用效果还不错，简单用到了WiFi AP网络编程、CH340、光敏传感器、ST7735字库。感觉把自己能做的都弄差不多了，搜到esp32还能使用micro-ros，ros是个很出名的机器人开源项目，提供了各种各样的机器人开发服务，以及硬件抽象、常用函数等。就想着就把ros也装到esp32s3上测试测试，了解一下。正巧DDS也被ros2采用，esp32这种边缘设备，要是能通过DDS的方式进行通信，那就大大免去了网络通信等一系列网络通信的复杂。



## 环境：

- vmware ubuntu24.04
- vscode
- esp32s3n16r8

## 安装vscode

## 在vacode中安装platformio插件

## 在Ubuntu系统中安装jazzy

- 必须使用Ubuntu24.04，ubuntu22.04也不行
- 安装完成后，每次使用需要**source /opt/ros/jazzy/setup.bash**更新才能使用ros2

[Installation — ROS 2 Documentation: Jazzy documentation](https://docs.ros.org/en/jazzy/Installation.html)

## 使用micro-ros

github仓库 [micro-ROS/micro_ros_platformio: micro-ROS library for Platform.IO](https://github.com/micro-ROS/micro_ros_platformio)  ，先根据README中安装依赖。

创建一个esp32s3的platformio项目，修改项目的的platformio.ini：

```ini
[env:esp32-s3-devkitc-1]
platform = espressif32
board = esp32-s3-devkitc-1
framework = arduino
;board_build.partitions = default_16MB.csv
board_build.mcu = esp32s3
board_build.arduino.memory_type = qio_opi
board_build.f_flash = 80000000L
board_build.flash_mode = dio
;board_microros_transport = serial	;使用串口传输
board_microros_transport = wifi		;使用wifi传输
monitor_speed = 115200
lib_deps =
    https://github.com/micro-ROS/micro_ros_platformio
```

### 测试串口

测试代码参考：[micro_ros_platformio/examples/micro-ros_publisher/src/Main.cpp at main · micro-ROS/micro_ros_platformio](https://github.com/micro-ROS/micro_ros_platformio/blob/main/examples/micro-ros_publisher/src/Main.cpp)

将参考代码直接复制到项目的main.cpp中即可

编译、烧录

使用命令**docker run -it --rm -v /dev:/dev --privileged --net=host microros/micro-ros-agent:jazzy serial --dev [YOUR BOARD PORT /dev/ttyUSB0] -v6**查看是否能收到消息（注意修改-dev参数）

![image-20241121234006354](https://tidcl.github.io/img/posts/micro-ros.assets/image-20241121234006354.png)

### 测试wifi

测试代码参考：[micro_ros_platformio/examples/micro-ros_publisher/src/Main.cpp at main · micro-ROS/micro_ros_platformio](https://github.com/micro-ROS/micro_ros_platformio/blob/main/examples/micro-ros_publisher/src/Main.cpp)

修改串口为wifi：

```c
//set_microros_serial_transports(Serial); //注释掉

//新增初始化wifi函数
void useWifi(){
  IPAddress agent_ip(192, 168, 31, 191);//micro-ros代理的ip地址
  size_t agent_port = 8888;//micro-ros代理的端口

  char ssid[] = "Xiaomi_2F1D";//连接wifi的名字
  char psk[]= "12345qwe";//连接wifi的密码

  set_microros_wifi_transports(ssid, psk, agent_ip, agent_port);
}
```

### 启动micro-ros代理

因为esp32上的micro-ros并不是真正的ros2节点，因此需要一个在局域网中的主机作为micro-ros的代理，esp32与代理交互，再接入ros2的DDS网络

![image-20241121232413188](https://tidcl.github.io/img/posts/micro-ros.assets/image-20241121232413188.png)

### 启动单片机

![image-20241121232651285](https://tidcl.github.io/img/posts/micro-ros.assets/image-20241121232651285.png)

对比看，此处传输已经变成了UDP，跟上面的SER串口比起来，传输方式已经变了。

使用命令**ros2 node list**查看dds网络中的节点，我启动官方示例的talker和listener，因此能看到三个节点

![image-20241121234147329](https://tidcl.github.io/img/posts/micro-ros.assets/image-20241121234147329.png)

## 自定义消息

- [How to include a custom ROS message in micro-ROS | micro-ROS](https://micro.ros.org/docs/tutorials/advanced/create_new_type/) micro-ros
- [Creating custom msg and srv files — ROS 2 Documentation: Jazzy documentation](https://docs.ros.org/en/jazzy/Tutorials/Beginner-Client-Libraries/Custom-ROS2-Interfaces.html#test-the-new-interfaces) ros2



## 错误

1. 因为我是使用虚拟机，连接效果时断时续经常出现烧录错误找不到等情况，拔掉重插直到出现
   ![image-20241121234833880](https://tidcl.github.io/img/posts/micro-ros.assets/image-20241121234833880.png)
   也可以不物理拔出，直接在vmware的右下角，进行连接/断开![image-20241121235038035](https://tidcl.github.io/img/posts/micro-ros.assets/image-20241121235038035.png)
2. 如果遇到加载慢或者无响应，大部分都是网络卡了，等就是了