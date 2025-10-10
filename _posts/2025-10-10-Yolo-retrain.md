---
layout:     post
title:      自定义模型教程--yolo11 Edge部署
subtitle:    "\"reTrain Yolo11 model Deployment Edge\""
date:       2025-10-10
author:     Parker
header-img: img/post-bg-coffee.jpeg
catalog: true
tags:
    - Vision AI
    - Yolo
    - Edge
    - Camera
    - 自定义模型
---

# 自定义模型教程--yolo11 Edge部署

请关注reCamera项目，一款开源的AI相机[Seeed-Studio/OSHW-reCamera-Series: reCamera is an opensource camera platform](https://github.com/Seeed-Studio/OSHW-reCamera-Series)

> 模型自定义分为三个主要部分，如您已熟悉yolo11的训练，可以跳转学习观看：

1. [数据集制作与获取](#数据集制作与获取)
2. [yolo模型训练](#云服务模型训练)
3. [模型量化和部署](#模型量化以及转换部署)

# 数据集制作与获取

## 下载云训练平台 Roboflow 数据集

Roboflow 推理服务器支持多种计算机视觉模型的数据集下载（免费）、训练和部署（付费）。用户也可以上传自己的数据集进行标注后训练（付费）。我们这里只下载它的免费数据集，后续用自己服务器训练。

### 网络

**注意**：下载数据时先不要插recamera。

打开网站地址：<https://universe.roboflow.com/>


### 登录

可以用github账号登陆，也可以用邮箱。

![演示图](https://gitee.com/huhuizhong/images/raw/master/images/202510101756994.png)


### 下载公开数据集

搜索你要的数据集名字，尽量选择1000-2000图片数的数据集。

![演示图](https://gitee.com/huhuizhong/images/raw/master/images/202510101756996.png)



点击数据集，等待一下，等页面完全加载，你可能会看见“Fork project”或“Download project”，不要点击它们。在左侧工具栏找到“Dataset”,点击“Download dataset”：

![演示图](https://gitee.com/huhuizhong/images/raw/master/images/202510101756997.png)

![演示图](https://gitee.com/huhuizhong/images/raw/master/images/202510101756998.png)



选择第三个：

![演示图](https://gitee.com/huhuizhong/images/raw/master/images/202510101756999.png)



选择YOLOv11和下载压缩包，点击continue：

![演示图](https://gitee.com/huhuizhong/images/raw/master/images/202510101756000.png)

![演示图](https://gitee.com/huhuizhong/images/raw/master/images/202510101756001.png)



最后出现的这个不用管他，直接叉掉：

![演示图](https://gitee.com/huhuizhong/images/raw/master/images/202510101756003.png)



你会看到浏览器上已经下载好数据包：

![演示图](https://gitee.com/huhuizhong/images/raw/master/images/202510101756004.png)




数据包含：

```
data/                  # 数据集根目录
├── test/              # 测试集
│   ├── images/    # 图片
│   └── labels/    # 标签
│── train/              # 训练集
│   ├── images/    # 图片
│   └── labels/    # 标签
│
│── valid/              # 验证集
│   ├── images/    # 图片
│   └── labels/    # 标签
│
│── data.yaml #配置；用于规定训练验证测试集路径、训练种类和说明数据信息
│
└── README.md  #说明文档
```



![演示图](https://gitee.com/huhuizhong/images/raw/master/images/202510101756005.png)

然后我们就可以进入训练环节。


data.yaml如下配置：

```
train: ../train/images # 根据你的实际地址做修改
val: ../valid/images
test: ../test/images

nc: 1
names: ['missing hole'] # 改成你自己的标签

roboflow:
  workspace: rf-projects
  project: pcb-holes
  version: 4
  license: CC BY 4.0
  url: https://universe.roboflow.com/rf-projects/pcb-holes/dataset/4
```

# 云服务模型训练

## 云服务容器租赁

[AutoDL算力云 | 弹性、好用、省钱。租GPU就上AutoDL](https://www.autodl.com/console/homepage/personal)

推荐使用AutoDL云服务，在算力市场板块租赁带GPU的服务器，可以按小时使用收费。

![image-20250707144658793](https://gitee.com/huhuizhong/images/raw/master/images/202508111735478.png)

请按如图镜像配置来租赁服务器，然后电机创建并开机

![image-20250707144838287](https://gitee.com/huhuizhong/images/raw/master/images/202508111735480.png)

在控制台-->容器实例中找到你创建的服务器，注意在不适用的时候及时关机，以节约费用。

![image-20250707144948849](https://gitee.com/huhuizhong/images/raw/master/images/202508111735481.png)

服务器开机后会生成登录指令和密码：
![image-20250707145153234](https://gitee.com/huhuizhong/images/raw/master/images/202508111735482.png)

如图所示，复制出来的内容如下：

```bash
ssh -p 27189 root@connect.bjb1.seetacloud.com
OFQuH9acFE/1
```

在终端中进行命令输入直接登录即可，密码输入时没有输入显示，因为在Linux中输入密码是隐藏保护的。

![image-20250707145406814](https://gitee.com/huhuizhong/images/raw/master/images/202508111735483.png)

登录成功后按`本地部署深度学习环境`的教程文档进行部署即可搭建相应的环境。

## 云服务使用以及模型训练

从与服务中获得的登录指令和密码如下：

```bash
ssh -p 27189 root@connect.bjb1.seetacloud.com
OFQuH9acFE/1
```

其中@后面的`connect.bjb1.seetacloud.com`是登录链接，root是用户名，27189是端口号。
推荐下载[MobaXterm](https://mobaxterm.mobatek.net/download.html)来登录服务器，这里有可视化的文件管理工具来便于文件传输。
推荐下载安装版：
![image-20250707151931314](https://gitee.com/huhuizhong/images/raw/master/images/202508111735485.png)

打开MobaXterm，首先点击Session，选择ssh登录方式，在host,Username和Port分别填选从服务器中获取的登录指令信息。

host中填写`@`后的链接（例如：`connect.bjb1.seetacloud.com`），Username填写`root`，Port填写`-p`后的端口号(例如`27189`)

```bash
ssh -p 27189 root@connect.bjb1.seetacloud.com
OFQuH9acFE/1
```

完成后点击OK.

![image-20250707152109117](https://gitee.com/huhuizhong/images/raw/master/images/202508111735486.png)

### 上传数据集并进行训练

将你打包好的数据集上传到该文件下`/root/Download`,如同所示

![image-20250811140300448](https://gitee.com/huhuizhong/images/raw/master/images/202508111735487.png)

执行`cd ~/Download` 然后执行`./train_yolo11 你的数据集文件名（压缩包或解压缩文件都可）`

然后根据菜单要求填写，即可进行模型训练

![image-20250811140407081](https://gitee.com/huhuizhong/images/raw/master/images/202508111735488.png)

训练完成后，你可以在该文件夹下找到你的模型文件`/root/Download/weights/`

我们需要下载temp_downgraded.onnx文件到本地

![image-20250811140717426](https://gitee.com/huhuizhong/images/raw/master/images/202508111735489.png)

#### MobaXterm文本编辑器替换本地文本编辑器

如果想修改你的模型训练过程参数，点击setting-->General 然后选择本地的文本编辑器的路径或快捷打开方式即可

![image-20250707160448500](https://gitee.com/huhuizhong/images/raw/master/images/202508111735490.png)

打开train.py脚本，修改参数，然后按ctrl+s保存文件：

这里注意训练模型的配置脚本：

```python
data="/root/ultralytics/dataset/FYP/data.yaml", #个人数据集的配置路径
epochs=100,     #训练轮数，建议100~300之间，避免太多过拟合
imgsz=640,		#图片训练尺寸，这个参数不要修改
device="0",		#训练的显卡编号，如果是CPU，则填入cpu,如果你是多卡训练，可以填入多个显卡编号，用逗号隔开
batch=128,      # 单轮训练图片批量大小，48G显卡最多选128
amp=False,     # 禁用混合精度，不要修改该参数
cache=True     # 缓存图像以加快加载速度，不要修改该参数
```

![image-20250811144744375](https://gitee.com/huhuizhong/images/raw/master/images/202508111735491.png)

弹出是否同步到服务器中，选择Yes:
![image-20250707160851795](https://gitee.com/huhuizhong/images/raw/master/images/202508111735493.png)

### 模型效果测试

编辑测试脚本`yolo11_localPic.py`，将模型文件路径和测试用的图片换成你的模型和测试图片，然后执行该脚本可以获得测试结果。

![image-20250707165829911](https://gitee.com/huhuizhong/images/raw/master/images/202508111735494.png)

![image-20250707165958808](https://gitee.com/huhuizhong/images/raw/master/images/202508111735495.png)

![image-20250707170039535](https://gitee.com/huhuizhong/images/raw/master/images/202508111735496.png)

# 模型量化以及转换部署

## 模型量化

参考这篇[wiki](https://wiki.seeedstudio.com/recamera_model_conversion/)来搭建相关的环境，[中文版](https://blog.csdn.net/D777777777777/article/details/149200276?spm=1001.2014.3001.5502)

如果你不想搭建环境，并且使用windows系统。那你可以参考这篇资料来挂在WSL系统来完成后续的工作。镜像在资料包中。

[全网最全Win10/11系统下WSL2+Ubuntu20.04的全流程安装指南（两种支持安装至 D 盘方式）_win10安装wsl2-CSDN博客](https://blog.csdn.net/Natsuago/article/details/145594631)

![image-20250708162929318](https://gitee.com/huhuizhong/images/raw/master/images/202508111735497.png)

导入镜像资料后，使用`WSL -d Ubuntu-tpumlir`来启动系统
进入系统后执行下面的指令：

```bash
docker start 299 #启动docker容器
docker exec -it 299 /bin/bash #进入容器环境
cd /workspcae/tpu-mlir  #进入工程目录
```

你需要组织一个转换工程文件，文件名可以任意命名，内容参考如下：

```bash
model_yolo11n/          # 模型工作目录（与tpu-mlir同级）
├── COCO2017/           # COCO2017数据集（用于量化校准）替换为你的训练图片，大概需要100张左右
├── image/              # 测试图像目录 需要几张图片用来测试
└── Workspace/          # 实际工作子目录
    └── yolo11n.onnx    # 待转换的ONNX模型文件
```



#### ONNX→MLIR转换

##### 转换概述

ONNX 到 MLIR 的转换是模型转换过程中的关键中间步骤。在获得适用于 reCamera 推理的最终模型之前，需要先将 ONNX 模型转换为 MLIR 格式。这个 MLIR 文件作为桥梁，用于生成最终针对 reCamera 推理引擎优化的模型。

在 `Workspace` 目录下执行以下命令：

将model_def参数替换为自己的模型名称，test_input参数替换为自己的图片名称

```bash
model_transform \
  --model_name yolo11n \
  --model_def model_v8.onnx \
  --input_shapes "[[1,3,640,640]]" \
  --mean "0.0,0.0,0.0" \
  --scale "0.0039216,0.0039216,0.0039216" \
  --keep_aspect_ratio \
  --pixel_format rgb \
  --output_names "/model.23/cv2.0/cv2.0.2/Conv_output_0,/model.23/cv3.0/cv3.0.2/Conv_output_0,/model.23/cv2.1/cv2.1.2/Conv_output_0,/model.23/cv3.1/cv3.1.2/Conv_output_0,/model.23/cv2.2/cv2.2.2/Conv_output_0,/model.23/cv3.2/cv3.2.2/Conv_output_0" \
  --test_input ../image/0101.jpg \
  --test_result yolo11n_top_outputs.npz \
  --mlir yolo11n.mlir
```

![image-20250708145835686](https://gitee.com/huhuizhong/images/raw/master/images/202508111735498.png)

![image-20250708145856076](https://gitee.com/huhuizhong/images/raw/master/images/202508111735499.png)

#### 生成校准表

在转换为 INT8 模型之前，您需要运行 calibration 以获得校准表。根据情况，输入数据的数量约为 100 到 1000 个。 然后使用校准表生成对称或不对称 。如果对称模型已经满足要求，一般不建议使用不对称模型，因为不对称模型的性能稍逊于对称模型。这是一个现有的100幅图像进行校准的例子：cvimodel COCO2017

```bash
run_calibration \
  yolo11n.mlir \
  --dataset ../COCO2017/images \
  --input_num 100 \
  -o yolo11n_calib_table
```

![image-20250708151924542](https://gitee.com/huhuizhong/images/raw/master/images/202508111735500.png)

**编译INT8模型**：
获取文件后，执行以下命令将其转换为 INT8 对称量化模型

```bash
model_deploy \
  --mlir yolo11n.mlir \
  --quantize INT8 \
  --quant_input \
  --processor cv181x \
  --calibration_table yolo11n_calib_table \
  --test_input ../image/0101.jpg \
  --test_reference yolo11n_top_outputs.npz \
  --customization_format RGB_PACKED \
  --fuse_preprocess \
  --aligned_input \
  --model banana.cvimodel
```

![image-20250708155007113](https://gitee.com/huhuizhong/images/raw/master/images/202508111735501.png)

下载这个`cvimodel`结尾的模型到你的电脑上，使用usb线将recamera连接到你的电脑上，在浏览器中打开recamera的workspace界面
`http://192.168.42.1/#/workspace`。 选择model模块，点击Upload选择你下载好的`cvimodel`的模型。在下方的Classes中填写你用来分类的物体的名称。点击Deploy即可部署。

![image-20250708155239849](https://gitee.com/huhuizhong/images/raw/master/images/202508111735502.png)

访问`http://192.168.42.1/#/dashboard`或使用preview模块，然后你就可以预览到你的模型识别的效果了
![image-20250708155504625](https://gitee.com/huhuizhong/images/raw/master/images/202508111735503.png)

![image-20250709170145140](https://gitee.com/huhuizhong/images/raw/master/images/202508111735504.png)