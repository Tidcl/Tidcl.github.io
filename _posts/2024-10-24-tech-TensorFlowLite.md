---
title: "ESP32S3实现手势识别，edge impulse"
subtitle: " "
layout: post
author: "Tidcl"
header-style: text
hidden: false
plchart: true
tags:
  - ESP32S3
  - edge impulse
  - roboflow
  - colab

---





# 在经历了下面的所有步骤后

测试过esp8266后，突发奇想买了个esp32进行使用，在arduino上成功运行后，又探索了espidf和platformio，感觉到嵌入式设备在现在开发过程中，代码和环境已经不是最主要的了，自动化构建工具和包管理器已经比最开始学习嵌入式开发好了许多。但是目前很多教程还在教学从新建文件夹到导入库，构建项目目录，编写main.c巴拉巴拉。现在写代码最主要的难道不是将业务写成代码，实现功能吗？就像是现在大学一提到软件专业就是，c语言基础。。。

在实现了web服务后，翻找资料看到esp32s3有单精度浮点运算单元，也找到了一个ai的示例，就想着结合摄像头实现一个简单的ai摄像头应用，哪知道，这一试就花了不少时间。现实是大多数资料和文章都是三、四年前编写的，最初我找到pytorch、tensoflown等教程，好不容易训练出模型发现太大，后来才知道模型的大小不是根据数据量决定的，是模型本身决定。之后又训练出了一个mobilenet小模型，但是运行的时候又因为esp idf的版本导致崩溃。像是最小的yolo嵌入式模型都要几十MB，这明显是给树莓派这类设备使用的。于是又找到tensorflow，才发现tensorflow又改成了litert，又去找到litert发现最新的资料也是一两年的了。在逛了一堆ai论坛和应用后，我发现esp32s3的资料屈指可数，但是像stm32、Raspberry Pi文章又新又多。在经历了几天的查找后，我已经觉得在esp32s3运行图像识别已经不可能了，后来是在youtube上看到一个一年前的视频，但是也不是完整教程。博主只是说了大概步骤，但是我找到了edge impulse这个网站。

先在前面说明，下面只是我探索esp32s3运行手势检测模型的记录，最后是成功了，但是下面的探索过程已经没有参考意义，因为大多数资料都已经太久，或者仓库早已没维护。从最开始用roboflow收集数据集，再到colab训练模型，最后到模型量化压缩，再到各种各样sdk版本问题。最后在edgeImpulse https://studio.edgeimpulse.com/ 实现了，从**收集数据，标记数据，到训练数据**，最终选择编程语言和模型入门都很简单，要做的就只需要跟着一步步走，最后在esp32s3中运行起来就行了。虽然经过了非常多的曲折，但是最后的实现效果还不错，看到了边缘设备和物联网的无限潜力。



























































这下面的内容都是探索时剩下的，已经不想删除了。

## 姿态检测模型



## 转为*.h *.c模型



## esp32s3使用tensorflow加载*.c模型，进行识别





## LiteRT与TensorFlowLite

用于在移动设备、嵌入式设备和物联网（IoT）设备上运行机器学习模型的高性能运行时。LiteRT 不仅支持 TensorFlow 模型，还支持 PyTorch、JAX 和 Keras 等框架的模型，使其能够在各种设备上以优异的性能运行。



## 查找模型

模型网站，已经自动选择了LiteRT类型模型：[Find Pre-trained Models | Kaggle](https://www.kaggle.com/models?framework=tfLite)

yolo5模型下载地址：https://www.kaggle.com/api/v1/models/kaggle/yolo-v5/tfLite/tflite-tflite-model/1/download



## 构建和模型转换官方文档

[构建和转换模型  | Google AI Edge  | Google AI for Developers](https://ai.google.dev/edge/litert/microcontrollers/build_convert?hl=zh-cn)

[模型转换概览，将TensorFlow模型转换为LiteRT模型  | Google AI Edge  | Google AI for Developers](https://ai.google.dev/edge/litert/models/convert?hl=zh-cn)

python3.13.x 安装 tensorflow 报错：

```shell
D:\File\tensorflowmodel>pip install tensorflow
ERROR: Could not find a version that satisfies the requirement tensorflow (from versions: none)
ERROR: No matching distribution found for tensorflow
```

安装python3.11.9解决，[Python 3.11.9 下载地址| Python.org](https://www.python.org/downloads/release/python-3119/)



### 没有xxd工具，不能执行转换

xxd是用于转换和查看文件内容的命令行工具，通常用于十六进制（hex）和ASCII格式的转换。可以将二进制转为十六进制、十六进制转为二进制、**生成c语言数组**。

我这边是通过msys2安装vim：pacman -S vim，xxd通常包含在vim包中，安装好vim就有xxd工具。

#### 转换后的模型过于大

![image-20241018002342346](D:\File\文档\arduino\TensorFlowLite.assets\image-20241018002342346.png)

7M转成了45M，肯定放不进esp32s3里面了。

只能考虑自己训练了。

参考TensorFlow Lite的标准模型person_detect.tflite

```shell
xxd -i person_detect.tflite > person_detect_model_data.cc
```

person_detect.tflite 文件大小 294KB 转换后是 1876KB。



## 训练模型

[figure > Dataset Analytics (roboflow.com)](https://app.roboflow.com/peerchuan-tang/figure-b1fj2/health)

```
不想训练再去找小模型。找不到，下载别人的数据集在本地训练吧。
```

```shell
本地训练命令两个效果：
python train.py --img 320 --batch 16 --epochs 50 --data D:/File/tensorflowmodel/数据集/手势/data.yaml --weights yolov7.pt
python D:\File\tensorflowmodel\yolov7\train.py --img 320 --batch 16 --epochs 50 --data data.yaml --weights yolov7.pt
本地训练放弃
```

```
尝试使用 https://colab.research.google.com/ 训练
```



## 使用colab将pytorch模型转换成tflite，结构模型反而变大了



# 跟着官方的训练教程走

[微控制器使用入门  | Google AI Edge  | Google AI for Developers](https://ai.google.dev/edge/litert/microcontrollers/get_started?hl=zh-cn)

[tflite-micro/tensorflow/lite/micro/examples/person_detection/training_a_model.md at main · tensorflow/tflite-micro (github.com)](https://github.com/tensorflow/tflite-micro/blob/main/tensorflow/lite/micro/examples/person_detection/training_a_model.md)



```
#from tensorflow.contrib import training as contrib_training
from tensorboard.plugins.hparams import api as contrib_training
```



## 最终还是参考roboflow的训练文章：

[如何在自定义数据集上训练 MobileNetV2 --- How to Train MobileNetV2 On a Custom Dataset (roboflow.com)](https://blog.roboflow.com/how-to-train-mobilenetv2-on-a-custom-dataset/)

注意数据类型要选Classification，下载选择Folder Structure，选择Terminal [Roboflow Universe: Open Source Computer Vision Community](https://universe.roboflow.com/search?q=animal+classification)

## 切换colab的python版本

使用conda，但是繁琐 [Tensorflow 1.15 更新后无法使用 · 问题 #3266 · googlecolab/colabtools --- Tensorflow 1.15 impossible to use after update · Issue #3266 · googlecolab/colabtools (github.com)](https://github.com/googlecolab/colabtools/issues/3266#issuecomment-1348660900)

[【colab】python3.6的使用（两步，切换python version并安装pip）_google clab转换python版本-CSDN博客](https://blog.csdn.net/weixin_42815846/article/details/128475759)

```
dpkg: error processing package python3-pip (--configure):
 dependency problems - leaving unconfigured
Errors were encountered while processing:
 python3-setuptools
 python3-wheel
 python3-pip
E: Sub-process /usr/bin/dpkg returned an error code (1)

cd /var/lib/dpkg/  # 进入这个路径
sudo mv info/ info_bak  # 将info文件夹改名为info_bak
sudo mkdir info # 新建info文件夹
```

```
python -m pip install --upgrade pip
/usr/local/bin/python: No module named pip

直接编译安装，把整个环境换掉
sudo apt-get install build-essential checkinstall
sudo apt-get install libreadline-gplv2-dev libncursesw5-dev libssl-dev libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev
wget https://www.python.org/ftp/python/3.6.9/Python-3.6.9.tgz
tar xzf Python-3.6.9.tgz
cd Python-3.6.9
sudo ./configure --enable-optimizations
sudo make
sudo make altinstall
```

分类 [Classification with MobileNetV2.ipynb - Colab (google.com)](https://colab.research.google.com/drive/1bOzVaDQo8h6Ngstb7AcfzC35OihpHspt?usp=sharing&ref=blog.roboflow.com#scrollTo=iBMcobPHdD8O)

对象检测 

## MobileNet模型训练终有突破

为找colab改python版本，conda管理python版本都不行。索性还是找新文章使用新的tensorflow，就不跟tensorflow1.15 python3.6杠了。

搜索引擎关键字：colab tensorflow lite

分类文章 https://universe.roboflow.com/ds/vwpap9iRlS?key=q4hUNNSBbj

官方文档 [使用 TensorFlow Lite Model Maker 进行目标检测](https://www.tensorflow.org/lite/models/modify/model_maker/object_detection?hl=zh-cn)

[TensorFlow Lite 模型制作工具  | Google AI Edge  | Google AI for Developers](https://ai.google.dev/edge/litert/libraries/modify?hl=zh-cn)

[训练和使用您自己的模型  | Vertex AI  | Google Cloud](https://cloud.google.com/vertex-ai/docs/training-overview?hl=zh-cn#image_data)

## 真正训练出来几百KB模型

参考视频 [Adding AI to your ESP32 is Easier than You Think! (youtube.com)](https://www.youtube.com/watch?v=ILh38jd0GNU)

colab图像分类参考   [train_tiny_mobilenet.ipynb](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbVdRZUxqOVlINWx2SmI5cTJidnpDczVIUW1NQXxBQ3Jtc0trNHBEbVgyR2I1M2dBaFktSTl1LVU5YnBreUlUbUo1ZkoxektqZ0ExX1Jsb2ExWHdzaUYxUkN5N2FyelYxTldFSkRBZHN2azVoT1lJMlhVaGlqV3o2ek9xX29nYWtHcDAwYXh4Ujg3NjE2V2QyelRCYw&q=https%3A%2F%2Fcolab.research.google.com%2Fdrive%2F1_LkJ_4GonJWmUc17mM-hVsK96HSdt2Fq%3Fusp%3Dsharing&v=ILh38jd0GNU)

## 都不可靠，自己再colab中使用pytorch训练

```
esp32s3运行时报错：
Didn't find op for builtin opcode 'PAD'
Failed to get registration from op code PAD

Didn't find op for builtin opcode 'ADD'
Failed to get registration from op code ADD

micro_op_resolver.AddAdd();
micro_op_resolver.AddPad();

操作符（Op） 是指模型执行的基本计算单元，比如加法（ADD）、卷积（Conv2D）、池化（AveragePool2D）、Softmax、Reshape 等。每个操作符执行特定的数学运算，组成了深度学习模型的核心。
```



heap_caps_malloc 运行报错内存不够，实际上

Free heap size: 8722608 bytes
Minimum free heap size: 8722608 bytes

这是因为如果 heap_caps_malloc 函数要使用PSRAM，需要加上 heap_caps_malloc(..., MALLOC_CAP_SPIRAM)



报错

```
Failed to allocate tail memory. Requested: 40, available 32, missing: 8
Failed to allocate memory for persistent TfLiteTensor
Failed to initialize input tensor 0
AllocateTensors() failed
```

解决

在此处多分配40字节

```
tensor_arena = (uint8_t *) heap_caps_malloc(kTensorArenaSize, MALLOC_CAP_SPIRAM);
```

## 新结果

[MobileViT：一种面向移动设备的基于 Transformer 的图像分类模型 - Keras 中文](https://keras.org.cn/examples/vision/mobilevit/) 训练文章

[TensorFlow Datasets](https://www.tensorflow.org/datasets/catalog/rock_paper_scissors) 现成数据集

## saved_model 转 tflite 崩溃

```
# https://github.com/keras-team/keras/issues/19108
# 使用keras api
import tensorflow as tf

import keras

(train_images, train_labels), (
    test_images,
    test_labels,
) = keras.datasets.fashion_mnist.load_data()

test_model = keras.Sequential(
    [
        keras.layers.Flatten(),
        keras.layers.Dense(64, activation="relu"),
        keras.layers.Dense(10, activation="softmax"),
    ]
)
test_model.compile(
    optimizer="adam",
    loss=keras.losses.SparseCategoricalCrossentropy(),
    metrics=["accuracy"],
)
test_model.fit(train_images, train_labels, epochs=1)
test_model.export("test", "D:/File/tensorflowmodel/trainOK/mobilevit_sjb")  # replace tf.saved_model.save with this line
converter = tf.lite.TFLiteConverter.from_saved_model("test")
tflite_model = converter.convert()
with open("model.tflite", "wb") as f:
    f.write(tflite_model)
```

## 新训练 2024年10月20日

[ImageNet-1k1 MobileVit](https://www.kaggle.com/code/stpeteishii/imagenet-1k1-mobilevit/notebook)

[Google Colab](https://colab.research.google.com/drive/1CloZrh6dsyqSUBQsWVGaNa9Fr3PtGY1A)

## onnx 转 tflite





