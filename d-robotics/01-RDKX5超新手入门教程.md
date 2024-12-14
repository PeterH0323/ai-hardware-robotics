# RDK X5超新手入门教程 ：从系统烧录到 YOLO 物体识别

## 目录

- [RDK X5超新手入门教程 ：从系统烧录到 YOLO 物体识别](#rdk-x5超新手入门教程-从系统烧录到-yolo-物体识别)
  - [目录](#目录)
  - [如何购买](#如何购买)
  - [RDK X5 规格参数](#rdk-x5-规格参数)
  - [系统烧录](#系统烧录)
    - [烧录准备](#烧录准备)
      - [供电](#供电)
      - [存储](#存储)
      - [显示](#显示)
      - [网络连接](#网络连接)
      - [系统烧录](#系统烧录-1)
    - [镜像下载](#镜像下载)
    - [系统烧录](#系统烧录-2)
    - [启动系统](#启动系统)
    - [常见问题](#常见问题)
    - [注意事项](#注意事项)
  - [YOLO算法测试](#yolo算法测试)
    - [运行方法](#运行方法)
  - [摄像头应用](#摄像头应用)
    - [摄像头图像本地保存](#摄像头图像本地保存)
    - [目标检测算法—YOLOv5s](#目标检测算法yolov5s)
  - [ROS 应用](#ros-应用)
    - [功能介绍](#功能介绍)
    - [使用 MIPI 摄像头发布图片](#使用-mipi-摄像头发布图片)
    - [结果分析](#结果分析)
      - [使用摄像头发布图片](#使用摄像头发布图片)


## 如何购买

第一步就是 买板子 ！大家可以去到 [RDK官网](https://developer.d-robotics.cc/rdkx5#:~:text=Ubuntu%2022.04-,%E8%B4%AD%E4%B9%B0%E6%B8%A0%E9%81%93,-%E2%80%A2%C2%A0%C2%A0%E5%AE%98%E6%96%B9%E8%AE%A4%E8%AF%81%E5%88%86%E9%94%80)进行购买

## RDK X5 规格参数

| 元器件 | 参数                                                                                                                                                                               |
| ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CPU    | 8xA55@1.5GHz                                                                                                                                                                       |
| RAM    | 4GB/8GB LPDDR4                                                                                                                                                                     |
| BPU    | 10 TOPS                                                                                                                                                                            |
| GPU    | 32Gflops                                                                                                                                                                           |
| 储存   | NA, supports external Micro SD card Peripheral                                                                                                                                     |
| 多媒体 | H.265 (HEVC) Main Profile @ L5.1, H.264 (AVC) Baseline/Constrained Baseline/Main/High Profiles @ L5.2 with SVC-T encoding, H.265/H.264 encoding and decoding up to 3840x2160@60fps |

RDK X5 提供了网口、USB、摄像头、LCD、HDMI、CANFD、40PIN等功能接口，方便用户进行图像多媒体、深度学习算法等应用的开发和测试。开发板接口布局如下：

![image](https://developer.d-robotics.cc/rdk_doc/assets/images/RDK_X5_interface-fccfc8c221e9eacb4e5d8f4d41b3f9f2.jpg)

| 序号 | 功能                     | 序号 | 功能                     | 序号 | 功能                    |
| ---- | ------------------------ | ---- | ------------------------ | ---- | ----------------------- |
| 1    | 供电接口 （USB Type C）  | 2    | RTC 电池接口             | 3    | 易连接口 （USB Type C） |
| 4    | 调试串口（Micro USB）    | 5    | 2 路 MIPI Camera 接口    | 6    | 千兆以太网口，支持 POE  |
| 7    | 4 路 USB 3.0 Type A 接口 | 8    | CAN FD 高速接口          | 9    | 40PIN 接口              |
| 10   | HDMI 显示接口            | 11   | 多标准兼容耳机接口       | 12   | 板载 Wi-Fi 天线         |
| 13   | TF卡接口（底面）         | 14   | LCD 显示接口（MIPI DSI） |      |                         |

## 系统烧录

### 烧录准备

#### 供电

RDK X5开发板通过 USB Type C 接口供电，需要使用支持 **5V/3A** 的电源适配器为开发板供电。

> [!NOTE]
> 
> 请不要使用电脑USB接口为开发板供电，否则会因供电不足造成开发板**异常断电、反复重启**等异常情况。
>

#### 存储

RDK X5开发板采用 Micro SD 存储卡作为系统启动介质，推荐至少 `8GB` 容量的存储卡，以便满足 Ubuntu 系统、应用功能软件对存储空间的需求。

**注意** SD 卡选择的型号，参考 1元/1G 的价格去买，我买过便宜的会导致系统启动失败！

#### 显示

RDK X5开发板支持HDMI显示接口，通过HDMI线缆连接开发板和显示器，支持图形化桌面显示。

#### 网络连接

RDK X5开发板支持以太网、Wi-Fi 两种网络接口，用户可通过任意接口实现网络连接功能。

#### 系统烧录

RDK套件目前提供 Ubuntu 22.04 系统镜像，可支持 Desktop 桌面图形化交互。

> [!CAUTION]
> 
> **RDK X5 Module**出厂已经烧写测试版本系统镜像，为确保使用最新版本的系统，<font color='Red'>建议参考本文档完成最新版本系统镜像的烧写</font>。
> 

### 镜像下载

点击 [**下载镜像**](https://archive.d-robotics.cc/downloads/os_images/rdk_x5/)，进入版本选择页面，选择对应版本目录，进入3.1.0版本系统下载页。

下载完成后，解压出Ubuntu系统镜像文件，如 `rdk-x5-ubuntu22-preinstalled-desktop-3.1.0-arm64.img`

> [!TIP]
> 
> - `desktop` ：带有桌面的Ubuntu系统，可以外接屏幕、鼠标操作
> - `server` ：无桌面的Ubuntu系统，可以通过串口、网络远程连接操作
> 

### 系统烧录

在烧录Ubuntu系统镜像前，需要做如下准备：

- 准备至少 `8GB` 容量的 Micro SD 卡
- SD 读卡器
- 下载镜像烧录工具[balenaEtcher](https://etcher.balena.io/), [balenaEtcher](https://etcher.balena.io/) 是一款支持 Windows/Mac/Linux 等多平台的 PC 端启动盘制作工具。

制作SD启动卡流程如下：

1. 打开 [balenaEtcher](https://etcher.balena.io/) 工具，点击`Flash from file`按钮，选择解压出来的 `rdk-x5-ubuntu22-preinstalled-desktop-3.1.0-arm64.img` 文件作为烧录镜像 

![image-flash-2](https://developer.d-robotics.cc/rdk_doc/assets/images/image-X3-Update-balena1-a54cac3bbf66265174d8f71de5cbec6d.png)

2. 点击`Select target`按钮，选择对应的 Micro SD 存储卡作为目标存储设备  

![image-flash-2](https://developer.d-robotics.cc/rdk_doc/assets/images/image-X3-Update-balena3-7ff1f77425e7b508c81b2d1ceff2f640.png)

3. 点击`Flash`按钮开始烧录，待工具提示`Flash Complete`时，表示镜像烧录完成，可以关闭 balenaEtcher 并取出存储卡

![image-flash-3](https://developer.d-robotics.cc/rdk_doc/assets/images/image-X3-Update-balena4-1741bb9f58f0d1ed04a402111bd75251.png)

### 启动系统

首先保持开发板**断电**，然后将制作好的存储卡插入开发板的 Micro SD 卡槽，并通过 HDMI 线缆连接开发板与显示器，最后给开发板上电。

系统首次启动时会进行默认环境配置，整个过程持续45秒左右，配置结束后会在显示器输出 Ubuntu 系统桌面。

> [!TIP]
> 开发板指示灯说明
> **<font color='Green'>绿色</font>** 指示灯：点亮代表硬件上电正常
> 
> 如果开发板上电后长时间没有显示输出（2分钟以上），说明开发板启动异常。需要通过串口线进行调试，查看开发板是否正常。
> 

Ubuntu Desktop 版本系统启动完成后，会通过 HDMI 接口在显示器上输出系统桌面，如下图：

![image-desktop_display.jpg](https://developer.d-robotics.cc/rdk_doc/assets/images/image-desktop_display-7b73c6fb12cd3b20372172194a712ec7.jpg)

如果是没烧录桌面版的同学，可以看下是否有 橙色灯 亮起，如果亮起则表明系统起来了，可以开始玩耍了！

### 常见问题

首次使用开发板时的常见问题如下：

- **<font color='Blue'>上电不开机</font>** ：请确保使用[供电](#供电)中推荐的适配器供电；请确保开发板的Micro SD卡已经烧录过Ubuntu系统镜像
- **<font color='Blue'>使用中热插拔存储卡</font>** ：开发板不支持热插拔Micro SD存储卡，如发生误操作请重启开发板


### 注意事项

- **禁止**带电时拔插除 USB、HDMI、网线之外的任何设备
- RDK X5 的 Type C USB 接口仅用作供电 
- 选用正规品牌的 USB Type C 口供电线，否则会出现供电异常，导致系统异常断电的问题

## YOLO算法测试

本示例主要实现以下功能：

1. 加载 `yolov5s_672x672_nv12` 目标检测模型
2. 读取 `kite.jpg` 静态图片作为模型的输入
3. 分析算法结果，渲染检测结果

### 运行方法

本示例的完整代码和测试数据安装在 `/app/pydev_demo/07_yolov5_sample/` 目录下，调用以下命令运行

```shell
cd /app/pydev_demo/07_yolov5_sample/
sudo python3 ./test_yolov5.py
```

运行成功后，会输出目标检测结果，并且输出渲染结果到`result.jpg`文件中，如下图：
![image-20220624105432872](https://developer.d-robotics.cc/rdk_doc/assets/images/image-20220624105432872-654432141a8874eac83e449856a17432.png)

## 摄像头应用

### 摄像头图像本地保存

本示例`vio_capture`示例实现了`MIPI`摄像头图像采集，并将`RAW`和`YUV`两种格式的图像本地保存的功能。示例流程框图如下：
![image-capture](../../../static/img/03_Basic_Application/04_multi_media/image/cdev_demo/image-capture.jpg)

- **环境准备：**

  - 开发板断电状态下，将 `MIPI` 摄像头接入开发板，连接方法可参考 -[硬件简介-MIPI接口](https://developer.d-robotics.cc/rdk_doc/Quick_start/hardware_introduction/rdk_x3#mipi_port)
  - 通过 HDMI 线缆连接开发板和显示器
  - 开发板上电，并通过命令行登录

- **运行方式：**
  示例代码以源码形式提供，需要使用`make`命令进行编译后运行，步骤如下：

  ```bash
  sunrise@ubuntu:~$ cd /app/cdev_demo/vio_capture/
  sunrise@ubuntu:/app/cdev_demo/vio_capture$ sudo make
  sunrise@ubuntu:/app/cdev_demo/vio_capture$ sudo ./capture -b 16 -c 10 -h 1080 -w 1920
  ```

  参数说明：

  - `-b`: RAW 图 bit 数，IMX219 / IMX477 / OV5647 都设置为 16，只有极少数 Camera Sensor 需要设置为 `8`
  - `-c`: 保存图像的数量
  - `-w`: 保存图像的宽度
  - `-h`: 保存图像的高度


- **预期效果：**
  程序正确运行后，当前目录保存指定数量的图片文件，`RAW` 格式以 `raw_*.raw` 方式命名，`YUV` 格式以 `yuv_*.yuv` 方式命名。运行log如下：

  ```bash
  sunrise@ubuntu:/app/cdev_demo/vio_capture$ sudo ./capture -b 16 -c 10 -h 1080 -w 1920
  [INFO] board_id is 301, not need skip sci1.
  Searching camera sensor on device: /proc/device-tree/soc/cam/vcon@0 i2c bus: 6 mipi rx phy: 0
  INFO: Found sensor name:ov5647 on mipi rx csi 0, i2c addr 0x36, config_file:linear_1920x1080_raw10_30fps_2lane.c
  2000/01/01 08:18:54.877 !INFO [CamInitParam][0139]Setting VSE channel-0: input_width:1920, input_height:1080,  dst_w:1920, dst_h:1080
  2000/01/01 08:18:54.877 !INFO [CamInitParam][0139]Setting VSE channel-1: input_width:1920, input_height:1080, dst_w:1920, dst_h:1080
  ... 省略 ...
  capture time :0
  temp_ptr.data_size[0]:4147200
  capture time :1
  temp_ptr.data_size[0]:4147200
  capture time :2
  temp_ptr.data_size[0]:4147200
  ... 省略 ...
  capture time :8
  temp_ptr.data_size[0]:4147200
  capture time :9
  temp_ptr.data_size[0]:4147200
  ov5647 sensor stop
  ```

### 目标检测算法—YOLOv5s

本示例基于`YOLOv5`模型，实现了摄像头目标检测算法功能，用户可通过显示器预览检测结果。

- **环境准备：**
   - 开发板断电状态下，将`MIPI`摄像头接入开发板，连接方法可参考-[硬件简介-MIPI接口](https://developer.d-robotics.cc/rdk_doc/Quick_start/hardware_introduction/rdk_x3#mipi_port)
  - 通过HDMI线缆连接开发板和显示器
  - 开发板上电，并通过命令行登录

 - **运行方式：**
    示例代码以源码形式提供，需要使用`make`命令进行编译后运行，步骤如下：

    ```bash
    sunrise@ubuntu:~$ cd /app/cdev_demo/bpu/src
    sunrise@ubuntu:/app/cdev_demo/bpu/src$ sudo make
    sunrise@ubuntu:/app/cdev_demo/bpu/src$ cd bin
    sunrise@ubuntu:/app/cdev_demo/bpu/src/bin$ sudo ./sample -f /app/model/basic/yolov5s_672x672_nv12.bin -m 0
    ```

    **参数配置：**

    - -f: 模型的路径
    - -m: 模型类型，默认为0


 - **预期效果：**
    程序正确运行后，会通过`HDMI`接口输出视频和算法检测渲染后的画面，用户可通过显示器预览。运行log如下：

    ```bash
    sunrise@ubuntu:/app/cdev_demo/bpu/src/bin$ sudo ./sample -f /app/model/basic/yolov5s_672x672_nv12.bin -m 0
    [BPU_PLAT]BPU Platform Version(1.3.6)!
    [HBRT] set log level as 0. version = 3.15.49.0
    [DNN] Runtime version = 1.23.8_(3.15.49 HBRT)
    [A][DNN][packed_model.cpp:247][Model](2000-01-01,19:06:39.821.214) [HorizonRT] The model builder version = 1.23.5
    [W][DNN]bpu_model_info.cpp:491][Version](2000-01-01,19:06:39.876.293) Model: yolov5s_v2_672x672_bayese_nv12. Inconsistency between the hbrt library version 3.15.49.0 and the model build version 3.15.47.0 detected, in order to ensure correct model results, it is recommended to use compilation tools and the BPU SDK from the same OpenExplorer package.
    Model info:
    model_name: yolov5s_v2_672x672_bayese_nv12Input count: 1input[0]: tensorLayout: 2 tensorType: 1 validShape:(1, 3, 672, 672, ), alignedShape:(1, 3, 672, 672, )
    Output count: 3Output[0]: tensorLayout: 0 tensorType: 13 validShape:(1, 84, 84, 255, ), alignedShape:(1, 84, 84, 255, )
    Output[1]: tensorLayout: 0 tensorType: 13 validShape:(1, 42, 42, 255, ), alignedShape:(1, 42, 42, 255, )
    Output[2]: tensorLayout: 0 tensorType: 13 validShape:(1, 21, 21, 255, ), alignedShape:(1, 21, 21, 255, )
    [INFO] board_id is 301, not need skip sci1.
    Searching camera sensor on device: /proc/device-tree/soc/cam/vcon@0 i2c bus: 6 mipi rx phy: 0
    INFO: Found sensor name:ov5647 on mipi rx csi 0, i2c addr 0x36, config_file:linear_1920x1080_raw10_30fps_2lane.c
    2000/01/01 19:06:40.012 !INFO [CamInitParam][0139]Setting VSE channel-0: input_width:1920, input_height:1080, dst_w:672, dst_h:672
    2000/01/01 19:06:40.013 !INFO [CamInitParam][0139]Setting VSE channel-1: input_width:1920, input_height:1080, dst_w:1920, dst_h:1080
    ... 省略 ...
    ```

## ROS 应用

### 功能介绍

YOLO目标检测算法示例使用图片作为输入，利用 BPU 进行算法推理，发布包含目标类别和检测框的算法msg。目前支持 YOLOv2、YOLOv3、YOLOv5、YOLOv5x 四个版本。

模型使用[COCO数据集](http://cocodataset.org/)进行训练，支持的目标检测类型包括人、动物、水果、交通工具等共 80 种类型。

代码仓库：https://github.com/D-Robotics/hobot_dnn

应用场景：YOLO系列作为单阶段目标检测中的代表算法，具有速度快，泛化性好的优点，可实现垃圾识别、车辆检测等功能，主要应用于自动驾驶、智能家居等领域。

### 使用 MIPI 摄像头发布图片

YOLOv2目标检测算法示例订阅MIPI摄像头发布的图片，经过算法推理后发布算法msg，通过 websocket package 实现在 PC 端浏览器上渲染显示发布的图片和对应的算法结果。


<Tabs groupId="tros-distro">
<TabItem value="foxy" label="Foxy">

```bash
# 配置tros.b环境
source /opt/tros/setup.bash
```

</TabItem>

<TabItem value="humble" label="Humble">

```bash
# 配置tros.b环境
source /opt/tros/humble/setup.bash
```

</TabItem>

</Tabs>

```shell
# 配置MIPI摄像头
export CAM_TYPE=mipi

# 启动launch文件
ros2 launch dnn_node_example dnn_node_example.launch.py dnn_example_config_file:=config/yolov2workconfig.json dnn_example_image_width:=480 dnn_example_image_height:=272
```

### 结果分析

#### 使用摄像头发布图片

在运行终端输出如下信息：

```text
[example-3] [WARN] [1655095347.608475236] [example]: Create ai msg publisher with topic_name: hobot_dnn_detection
[example-3] [WARN] [1655095347.608640353] [example]: Create img hbmem_subscription with topic_name: /hbmem_img
[example-3] [WARN] [1655095348.709411619] [img_sub]: Sub img fps 12.95
[example-3] [WARN] [1655095348.887570945] [example]: Smart fps 12.10
[example-3] [WARN] [1655095349.772225728] [img_sub]: Sub img fps 11.30
[example-3] [WARN] [1655095349.948913662] [example]: Smart fps 11.31
[example-3] [WARN] [1655095350.834951431] [img_sub]: Sub img fps 11.30
[example-3] [WARN] [1655095351.011915729] [example]: Smart fps 11.30
```

输出log显示，发布算法推理结果的topic为`hobot_dnn_detection`，订阅图片的topic为`/hbmem_img`。

在PC端的浏览器输入 `http://IP:8000` 即可查看图像和算法渲染效果（IP为RDK的IP地址）：

![render_web](/../static/img/05_Robot_development/03_boxs/detection/image/box_basic/yolov2_render_web.jpeg)
