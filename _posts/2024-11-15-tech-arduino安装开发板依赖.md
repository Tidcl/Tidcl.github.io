---
title: "arduino安装esp32s3的开发环境，ov2640摄像头使用"
subtitle: " "
layout: post
author: "Tidcl"
header-style: text
hidden: false
plchart: true
tags:
  - ESP32S3
  - arduino
  - ov2640
---





arduino论坛：https://forum.arduino.cc/t/esp32-s3-n16r8-new-board/1099353/9

手动安装arduino的esp32开发板管理器
“文件-首选项-其他开发板管理器地址”，添加：https://dl.espressif.com/dl/package_esp32_index.json

## 开发板介绍：esp32-s3-cam
开发板模组：Goouuu ESP32-S3 N16R8 基于乐鑫 ESP32-S3-WROOM-1 。

ESP32-S3-WROOM-1模组文档：https://www.espressif.com/sites/default/files/documentation/esp32-s3-wroom-1_wroom-1u_datasheet_cn.pdf

ESP32-S3芯片文档：[esp32-s3_datasheet_cn.pdf (espressif.com)](https://www.espressif.com/sites/default/files/documentation/esp32-s3_datasheet_cn.pdf)

![img](https://tidcl.github.io/img/posts/arduino安装开发板依赖.assets/ed4712c665eccfa2cf1bec3e7d5dbb1a.jpeg)

## **Description:**Multiprotocol Modules SMD module, ESP32-S3R8 with 8 MB Octal PSRAM die inside, 16 MB Quad SPI flash, IPEX antenna connector



### 开发板参考图

![img](https://tidcl.github.io/img/posts/arduino安装开发板依赖.assets/67d109cf63299b15de6fe69d7ffcd230-17291512049601.jpeg)

![img](https://tidcl.github.io/img/posts/arduino安装开发板依赖.assets/f7ed99237107a398e3e8b417f3841710.jpeg)

## arduino配置开发板参数，选择“工具”：
#### Flash Size：

16MB（128Mb）

#### Partition Scheme：

16M Flash

#### PSRAM：

OPI PSRAM

#### USB DFU On Boot：
使用**USB DFU（Device Firmware Upgrade）**引导模式，可以通过USB接口直接更新固件，而无需额外的串口转换器。这使得在没有外部硬件的情况下，通过USB直接进行固件升级成为可能。ESP32-S3原生支持USB OTG（On-The-Go），这意味着它既可以作为主机设备，也可以作为设备运行，比如通过USB DFU进行固件升级。

#### USB CDC On Boot：
通过 USB 实现串行通信的标准。ESP32-S3 支持原生 USB 功能，其中包括 USB CDC，可以让设备通过 USB 接口与主机通信，如同使用 UART 串口一样。这意味着你可以通过 USB 使用串口监视器和其他通信工具与设备进行交互，而不需要额外的串口转换器。
#### Flash Mode:
指定如何通过SPI与外部Flash存储进行通信的方式。
QIO:Quad SPI I/O 模式下使用 4 条SPI数据线进行输入和输出。
DIO:Dual SPI I/O 模式下使用 2 条SPI数据线进行输入和输出。
OPI:Octal Phase I/O 模式下使用 8 条SPI数据线进行输入和输出。
#### JTAG Adapter:
是一种用于访问和控制电子设备内部测试电路的接口适配器。JTAG“Joint Test Action Group”是一种国际标准测试协议，主要用于系统仿真、调试及芯片内部测试。
#### USB Firmware MSC On Boot：
允许ESP32-S3开发板在启动时通过USB接口以大容量存储设备（Mass Storage Class，MSC）的身份与计算机通信。这意味着当你使用USB线将ESP32-S3开发板连接到计算机时，计算机会识别它为一个USB存储设备，类似于U盘。

在下方给出我的配置：

![image-20241017155315441](https://tidcl.github.io/img/posts/arduino安装开发板依赖.assets/image-20241017155315441.png)

arduino编译出的文件位置：

## arduino创建多个文件，每个文件存放不同函数：
“项目-显示项目文件夹”，创建的*.h或*.c文件会自动显示在arduino窗口中

## 摄像头使用：

根据开发板蓝色针脚配置GPIO

![ESP32S3CAM-Pin](https://tidcl.github.io/img/posts/arduino安装开发板依赖.assets/ESP32S3CAM-Pin.jpg)

![img](https://tidcl.github.io/img/posts/arduino安装开发板依赖.assets/375f847d4c31fcec73606c97ffece3fd.jpeg)

摄像头代码

```c
#include "esp_camera.h"

bool initOV2640()
{
 // 配置摄像头引脚
  camera_config_t config;
  config.ledc_channel = LEDC_CHANNEL_0;
  config.ledc_timer = LEDC_TIMER_0;
  config.pin_d0 = 11;
  config.pin_d1 = 9;
  config.pin_d2 = 8;
  config.pin_d3 = 10;
  config.pin_d4 = 12;
  config.pin_d5 = 18;
  config.pin_d6 = 17;
  config.pin_d7 = 16;
  config.pin_xclk = 15;
  config.pin_pclk = 13;
  config.pin_vsync = 6;
  config.pin_href = 7;
  config.pin_sscb_sda = 4;
  config.pin_sscb_scl = 5;
  config.pin_pwdn = -1;
  config.pin_reset = -1;
  config.xclk_freq_hz = 20000000;
  config.pixel_format = PIXFORMAT_JPEG; // 支持 JPEG 和 RGB565
  config.frame_size = FRAMESIZE_UXGA;   // 分辨率，例如 UXGA (1600x1200)
  config.jpeg_quality = 10;             // JPEG质量（0到63），数值越小，质量越高
  config.fb_count = 1;                  // 帧缓冲区个数

  // 初始化摄像头
  esp_err_t err = esp_camera_init(&config);
  if (err != ESP_OK) {
    return false;
  }
  return true;
}

camera_fb_t* picOV2640()
{
  camera_fb_t *fb = esp_camera_fb_get();
  if (!fb) {
    Serial.println("捕获图像失败");
    return 0;
  }
  return fb;
}

// 释放帧缓冲区
void relPicOV2640(camera_fb_t* fb)
{
    if(fb) esp_camera_fb_return(fb);
}
```
