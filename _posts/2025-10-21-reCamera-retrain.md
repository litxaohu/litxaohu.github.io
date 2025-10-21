---
layout:     post
title:      reCamera 工作坊--yolo11
subtitle:    "\"reCamera 工作坊\""
date:       2025-10-21
author:     Parker
header-img: /img/reCamera_retrain/image-20251020165014299.png
catalog: true
tags:
    - Vision AI
    - Yolo
    - Edge
    - Camera
    - 自定义模型
---

# reCamera 工作坊



![image-20251020165014299](/img/reCamera_retrain/image-20251020165014299.png)

这是本次工作坊的流程图。若遇到任何问题，可向**工作人员**咨询，并说明出现问题时正在执行**哪一步骤、哪一选项**的操作，以便我们快速帮您定位问题。


## 第一步：数据集采集

### 选项 1：从Roboflow下载数据集

Roboflow 推理服务器支持多种计算机视觉模型的数据集下载（免费）、训练与部署（付费）。用户也可上传自有数据集进行标注和训练（付费）。本次仅下载免费数据集，并在自有服务器上开展训练。

#### 步骤 1：网络设置

**注意**：下载数据前请断开 reCamera 的连接。

访问链接：https://universe.roboflow.com/

#### 步骤 2：登录

使用 GitHub 账号或邮箱登录。

![演示](/img/reCamera_retrain/d1.png)

#### 步骤 3：下载公共数据集

搜索所需数据集名称，建议选择包含 1000-2000 张图像的数据集。

![演示](/img/reCamera_retrain/d2.png)

点击数据集并等待页面完全加载。页面中可能出现 “Fork project”（复刻项目）或 “Download project”（下载项目）—— 无需点击这两个选项。在左侧工具栏找到 “Dataset”（数据集），点击 “Download dataset”（下载数据集）：

![演示](/img/reCamera_retrain/d3.png)

选择第三个选项：

![演示](/img/reCamera_retrain/d5.png)

选择 “YOLOv11” 格式并下载压缩包，点击 “继续”：

![演示](/img/reCamera_retrain/d6.png)

直接关闭最终弹出的窗口，无需理会：

![演示](/img/reCamera_retrain/d8.png)

此时浏览器将开始下载数据集压缩包，可在下载列表中查看：

![演示](/img/reCamera_retrain/d9.png)

#### 数据集结构

```
data/                  # 数据集根目录
├── test/              # 测试集
│   ├── images/    # 图像文件
│   └── labels/    # 标签文件
│── train/              # 训练集
│   ├── images/    # 图像文件
│   └── labels/    # 标签文件
│
│── valid/              # 验证集
│   ├── images/    # 图像文件
│   └── labels/    # 标签文件
│
│── data.yaml # 配置文件：包含数据集路径、类别及元数据
│
└── README.md  # 文档说明
```

![演示](/img/reCamera_retrain/d10.png)

成功下载的 **.zip 压缩文件夹**，将在 [**步骤 2：训练 Yolo11n 模型**](# 步骤 2：训练 Yolo11n 模型) 中使用，现在可进入训练环节。

### 选项 2：使用CVAT平台标注数据集

如需手动标注自有图像，可通过 CVAT 平台完成标注，并以多种格式导出数据集。

#### 步骤 1：登录

访问链接：[cvat.ai](http://cvat.ai)

![演示](/img/reCamera_retrain/b1.png)

点击 “start for free”（免费开始），选择任一登录方式。

![演示](/img/reCamera_retrain/b2.png)

#### 步骤 2：创建项目与标签

1. 创建新项目，并添加标签名称（根据实际标注需求设置）：

![演示](/img/reCamera_retrain/b21.png)

#### 步骤 3：创建标注任务

1. 新建任务，设置任务名称、选择所属项目，然后上传待标注图像：

![演示](/img/reCamera_retrain/b3.png)

1. 进入 “Jobs”（任务）板块，点击图像进入标注界面：

![演示](/img/reCamera_retrain/b5.png)

#### 步骤 4：图像标注

1. 标注界面介绍：

![演示](/img/reCamera_retrain/b6.png)

1. 自动分割标注：使用 SAM 模型实现自动分割（适用于分割任务）：

![演示](/img/reCamera_retrain/b7.png)

1. 自动目标检测：使用自动检测工具（支持与 YOLO 兼容的目标，适用于检测任务）：

![演示](/img/reCamera_retrain/b8.png)

1. 手动标注：对于自定义目标，使用手动边界框工具标注：

![演示](/img/reCamera_retrain/b9.png)

1. 编辑标注：在右侧面板修改标注信息（如标签、坐标等）：

![演示](/img/reCamera_retrain/b10.png)

1. 保存标注：点击左上角按钮保存标注结果：

![演示](/img/reCamera_retrain/b11.png)

#### 步骤 5：完成任务与导出数据集

1. 将任务标记为 “完成”：

![演示](/img/reCamera_retrain/b14.png)

1. 导出数据集：点击导出按钮，选择 “Ultralytics YOLO Detection Track 1.0” 格式（适配 Yolo 模型）：

![演示](/img/reCamera_retrain/b12.png)

1. 下载数据集压缩包：

![演示](/img/reCamera_retrain/b13.png)

#### 步骤 6：整理数据集

下载的文件不含图像，需手动按以下结构整理：

![演示](/img/reCamera_retrain/s1.png)

参照 [“选项 1”](# 数据集结构) 的数据集结构，整理为如下格式：

![演示](/img/reCamera_retrain/s2.png)

#### 配置 data.yaml 文件

示例如下（需根据实际数据集路径和标签修改）：

```
train: ../train/images # 训练集图像路径，需根据实际路径调整
val: ../valid/images   # 验证集图像路径
test: ../test/images  # 测试集图像路径

nc: 1                  # 标签类别数量
names: ['missing hole'] # 标签名称，替换为实际标注标签

roboflow:
  workspace: rf-projects
  project: pcb-holes
  version: 4
  license: CC BY 4.0
  url: https://universe.roboflow.com/rf-projects/pcb-holes/dataset/4
```

最后，将整理好的数据集压缩为 **.zip 文件夹**，将在 [**步骤 2：训练 Yolo11n 模型**](# Step 2: Training Yolo11n model) 中使用。

![image-20250918153954024](/img/reCamera_retrain/image-20250918153954024.png)

### 选项 3：使用 SpireView 软件标注数据集（仅适用于 Windows）

如需手动标注自有图像，可使用 SpireView 软件完成标注，并以多种格式导出数据集。该软件需本地下载，目前仅兼容 Windows 系统。

- 下载链接：https://files.seeedstudio.com/wiki/reCamera/models/SpireView-v5.3.2.zip

- 项目开源地址：[jario-jin/SpireView](https://github.com/jario-jin/SpireView)

#### 步骤 1：创建数据集文件夹结构

1. 按以下结构创建空文件夹（图像可从网络下载或自行拍摄，对比例、格式、大小无特殊要求）：

```
data/                  # 数据集根目录
├── test/              # 测试集（占比 10%）
│── train/              # 训练集（占比 70%）
│── valid/              # 验证集（占比 20%）
│── data.yaml # 配置文件：包含数据集路径、类别及元数据
```

1. 将收集的图像按 1:7:2 的比例（测试集：训练集：验证集）分别放入对应文件夹。

#### 步骤 2：使用 SpireView 标注图像

1. 打开 SpireView 软件，对 test、train、valid 三个文件夹中的图像分别标注（标注方法一致，需注意切换文件夹路径）：

- 保存路径：设置为对应 “test”“train”“valid” 文件夹的目录；

- 图像路径：选择按比例划分好的图像所在目录。

![labeling.jpg](/img/reCamera_retrain/labeling.jpg)

![image-20250917144353471](/img/reCamera_retrain/image-20250917144353471.png)

1. 标注完成后，按键盘 “Ctrl + O”，选择 “Yolo 格式” 和 “Copy imgs”（复制图像）选项，点击 “确定”：

![image-20250917151313434](/img/reCamera_retrain/image-20250917151313434.png)

#### 步骤 3：整理标注结果

1. 标注后，仅保留目录中的 “Yolo_images” 和 “Yolo_labels” 文件夹，删除其他文件；

1. 将 “Yolo_images” 重命名为 “images”，“Yolo_labels” 重命名为 “labels”：

![image-20250917151732550](/img/reCamera_retrain/image-20250917151732550.png)

#### 步骤 4：配置 data.yaml 文件

在数据集根目录添加 data.yaml 文件，内容示例如下（需根据实际标签修改）：

```
train: ../train/images 
val: ../valid/images
test: ../test/images

nc: 2                                                          # 标签类别数量
names: ['hamburger','pizza']         # 标签名称，替换为实际标注标签
```

- “names” 中的标签名称需与标注时设置的标签一致（对应下图绿框内内容）：

![image-20250917152217435](/img/reCamera_retrain/image-20250917152217435.png)

#### 最终数据集结构

```
data/                  # 数据集根目录
├── test/              # 测试集
│   ├── images/    # 图像文件
│   └── labels/    # 标签文件
│── train/              # 训练集
│   ├── images/    # 图像文件
│   └── labels/    # 标签文件
│
│── valid/              # 验证集
│   ├── images/    # 图像文件
│   └── labels/    # 标签文件
│
│── data.yaml # 配置文件：包含数据集路径、类别及元数据
```

最后，将整理好的数据集压缩为 **.zip 文件夹**，将在 [**步骤 2：训练 Yolo11n 模型**](# 步骤 2：训练 Yolo11n 模型) 中使用。

#### （可选）使用 reCamera 获取图像数据集

阅读本部分前，请先了解 [选项 1：reCamera 基础款](# 选项 1：reCamera 基础款)。

可使用 reCamera 拍摄图像，标注为自有数据集，再训练并选择适配 reCamera 的模型。

![image-20250921185159946](/img/reCamera_retrain/image-20250921185159946.png)

![image-20250921190923722](/img/reCamera_retrain/image-20250921190923722.png)

![image-20250921185651232](/img/reCamera_retrain/image-20250921185651232.png)

![image-20250921190441369](/img/reCamera_retrain/image-20250921190441369.png)

![image-20250921190621305](/img/reCamera_retrain/image-20250921190621305.png)

访问 http://192.168.42.1/#/files 可预览和下载拍摄的照片：

![image-20250921190700927](/img/reCamera_retrain/image-20250921190700927.png)

- 双击图像可查看预览；

![image-20250921191052465](/img/reCamera_retrain/image-20250921191052465.png)

- 右键点击图像可选择 “下载” 或 “删除”。

![image-20250921191115213](/img/reCamera_retrain/image-20250921191115213.png)

## 第二步：训练 Yolo11n 模型

Ultralytics Yolo 已搭建用于模型训练的在线平台 [Ultralytics HUB](https://hub.ultralytics.com/signin)。通过上传数据集，可快速完成模型训练，并以多种格式导出模型文件。

### 步骤 1：登录平台

- 可使用 Google 账号、GitHub 账号、Apple ID 或邮箱注册新账号。

![image-20250918151625487](/img/reCamera_retrain/image-20250918151625487.png)

- 注册完成后，登录该平台。

- 登录成功后，注册所用的工作邮箱（WorkEmail）即为你的账号名。

![image-20250918152536455](/img/reCamera_retrain/image-20250918152536455.png)

- 若你的账号尚未升级至 **PRO 版本**，请将你的**账号信息**告知现场工作人员，他们将为你升级至 PRO 版本（有效期约 1 个月）。

![image-20250921183617436](/img/reCamera_retrain/image-20250921183617436.png)

### 步骤 2：上传数据集

![image-20250918155143713](/img/reCamera_retrain/image-20250918155143713.png)

![image-20250918155625232](/img/reCamera_retrain/image-20250918155625232.png)

![image-20250918155942638](/img/reCamera_retrain/image-20250918155942638.png)

![image-20250918160227678](/img/reCamera_retrain/image-20250918160227678.png)

**最终**，数据集上传完成后，页面将显示如下，此时可进入下一步操作。

![image-20250918160357106](/img/reCamera_retrain/image-20250918160357106.png)

### 步骤 3：创建项目

![image-20250918161844891](/img/reCamera_retrain/image-20250918161844891.png)

![image-20250918162050356](/img/reCamera_retrain/image-20250918162050356.png)

![image-20250918162202397](/img/reCamera_retrain/image-20250918162202397.png)

![image-20250918162237854](/img/reCamera_retrain/image-20250918162237854.png)

![image-20250918162404818](/img/reCamera_retrain/image-20250918162404818.png)

![image-20250918162612764](/img/reCamera_retrain/image-20250918162612764.png)

![image-20250918162821216](/img/reCamera_retrain/image-20250918162821216.png)

![image-20250918163030763](/img/reCamera_retrain/image-20250918163030763.png)

**最终**，项目创建完成后页面将显示如下，此时可进入下一步操作。

![image-20250918164440854](/img/reCamera_retrain/image-20250918164440854.png)

[**Ul**](https://hub.ultralytics.com/signin)[**tral**](https://hub.ultralytics.com/signin)[**ytic**](https://hub.ultralytics.com/signin)[**s HU**](https://hub.ultralytics.com/signin)[**B**](https://hub.ultralytics.com/signin) 平台按模型训练时长计费，价格优惠，欢迎大家使用该平台训练自定义模型。

![image-20250918165046842](/img/reCamera_retrain/image-20250918165046842.png)

### 步骤 4：下载 ONNX 模型

![image-20250918165608160](/img/reCamera_retrain/image-20250918165608160.png)

![image-20250918165743421](/img/reCamera_retrain/image-20250918165743421.png)

![image-20250918165952370](/img/reCamera_retrain/image-20250918165952370.png)

**最终**，需按以下结构整理文件，为下一步操作做准备：

- **COCO2017 文件夹**：用于存放校准数据集，需从你的[数据集](# 数据集结构)中复制 100 张图片放入该文件夹；

- **image 文件夹**：用于存放测试图片，需从你的[数据集](# 数据集结构)中复制 1 张图片，并将其重命名为 **"test.jpg"**；

- **Workspace 文件夹**：用于存放模型文件，将刚才下载的 ONNX 模型文件放入该文件夹。

```yaml
model_yolo11n/
├── COCO2017/           # 校准数据集（100 张图片）
|   └── Calibration1.jpg
|      bration2.jpg
|     	...
|    libration100.jpg
├──image/              # 测试图片（1 张图片）
|   └── test.jpg
└── Workspace/
    └── yolo11n_food.onnx    # ONNX 模型（刚下载的 ONNX 模型文件）          
```

文件准备完成后，进入 **[步骤 3：模型转换与部署](# 步骤 3：模型转换与部署)**

## 第三步：模型转换与部署

### 搭建工作环境

**注意**：你的计算机需运行 **Linux 系统**，或可使用 **WSL（Windows 子系统）**。

**若你不具备相关技术能力**，请将上一步准备好的文件夹发送给现场工作人员，并跳过 “步骤 3：模型转换与部署”。

工作人员将协助你完成必要的模型转换，并发送一个以 **“cvimodel”** 为后缀的文件给你，该文件将在 **[步骤 4：模型部署到reCamera](# 步骤 4：模型部署到reCamera)** 中使用。

#### 步骤 1：登录模型转换服务器

```
IP地址：95.179.234.194
用户名：recamera
密码：hebeucdio.413
```

![image-20250928144315143](/img/reCamera_retrain/image-20250928144315143.png)

![image-20250928144913120](/img/reCamera_retrain/image-20250928144913120.png)

![image-20250928145033818](/img/reCamera_retrain/image-20250928145033818.png)

进入 Docker 容器：

```
docker exec -it c4 /bin/bash
```

将文件移动到工作目录：

```
mv Download/model_yolo11n tpu-mlir/
cd tpu-mlir/model_yolo11n/workspace/
```

#### 步骤 2：准备工作空间

将上一步（即 **[步骤 4：下载 ONNX 模型](# 步骤 4：下载 ONNX 模型)**）准备好的 **model_yolo11n 文件夹**复制到 **tpu-mlir 目录**下。

```
cd model_yolo11n
cd Workspace
```

确保文件夹内无文件缺失，目录结构如下：

```yaml
model_yolo11n/
├── COCO2017/           # 校准数据集（100 张图片）
|   └── Calibration1.jpg
|      bration2.jpg
|     	...
|    libration100.jpg
├──image/              # 测试图片（1 张图片）
|   └── test.jpg
└── Workspace/
    └── yolo11n_food.onnx    # ONNX 模型（刚下载的 ONNX 模型文件）          
```

### 开始模型转换

#### 步骤 1：ONNX 转 MLIR 格式

**模型版本降级**：

```
python /workspace/tpu-mlir/downgrade_onnx.py yolo11n.onnx yolo11n_v8.onnx
```

在 Workspace 目录下执行以下命令：

```
model_transform \
  --model_name yolo11n \
  --model_def helmet_detect.onnx \
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

![image-20250928150842312](/img/reCamera_retrain/image-20250928150842312.png)

#### 步骤 2：生成校准表

```
run_calibration \
  yolo11n.mlir \
  --dataset ../COCO2017 \
  --input_num 100 \
  -o yolo11n_calib_table
```

![image-20250928151933553](/img/reCamera_retrain/image-20250928151933553.png)

#### 步骤 3：编译 INT8 模型

```
model_deploy \
  --mlir yolo11n.mlir \
  --quantize INT8 \
  --quant_input \
  --processor cv181x \
  --calibration_table yolo11n_calib_table \
  --test_input ../image/test.jpg \
  --test_reference yolo11n_top_outputs.npz \
  --customization_format RGB_PACKED \
  --fuse_preprocess \
  --aligned_input \
  --model yolo11n_sym_int8.cvimodel
  
model_deploy \
  --mlir yolo11n.mlir \
  --quant_input \
  --quantize F16 \
  --customization_format RGB_PACKED \
  --processor cv181x \
  --test_input ../image/0101.jpg \
  --test_reference yolo11n_top_outputs.npz \
  --fuse_preprocess \
  --tolerance 0.99,0.9 \
  --model yolo11n_1684x_f16.cvimodel
```

![image-20250928152130711](/img/reCamera_retrain/image-20250928152130711.png)

**最终**，将以 **cvimodel** 为后缀的模型文件下载到本地计算机，该文件将用于下一步操作。

![image-20250918172004887](/img/reCamera_retrain/image-20250918172004887.png)

**如需了解更多细节**，可参考完整在线资料：[w](https://wiki.seeedstudio.com/recamera_model_conversion/)[iki 文](https://wiki.seeedstudio.com/recamera_model_conversion/)[档](https://wiki.seeedstudio.com/recamera_model_conversion/) 或 [中文](https://blog.csdn.net/D777777777777/article/details/149200276?spm=1001.2014.3001.5502)[指南](https://blog.csdn.net/D777777777777/article/details/149200276?spm=1001.2014.3001.5502)

## **第四步：模型部署到reCamera**

### 选项 1：reCamera 基础款

- 确保你已准备好 reCamera 设备和一根 Type-C 数据线。

- ![image-20250916145844604](/img/reCamera_retrain/\image-20250916145844604.png)使用数据线将 reCamera 与电脑连接。

#### ![image-20250916150122897](/img/reCamera_retrain/\image-20250916150122897.png)步骤 2：将模型部署到 reCamera

- 访问 [192.168.42.1](http://192.168.42.1) 进入 reCamera 的加载页面。登录的 **用户名** 为：root；**密码** 为：recamera.1

![image-20250916163922968](/img/reCamera_retrain/image-20250916163922968.png)

![image-20250916164111563](/img/reCamera_retrain/image-20250916164111563.png)

![image-20250916164323357](/img/reCamera_retrain/image-20250916164323357.png)

![image-20250916164605325](/img/reCamera_retrain/image-20250916164605325.png)

![image-20250916165159291](/img/reCamera_retrain/image-20250916165159291.png)

![image-20250916165315002](/img/reCamera_retrain/image-20250916165315002.png)

![image-20250916165614859](/img/reCamera_retrain/image-20250916165614859.png)

![image-20250916170221913](/img/reCamera_retrain/image-20250916170221913.png)

```
http://192.168.42.1/#/workspace   //工作区链接，用于上传模型
http://192.168.42.1/#/dashboard   //拍摄预览视频链接
http://192.168.42.1/#/network     //后端网页链接，可配置网络设置、固件更新等

更多 reCamera 使用教程，请访问：https://wiki.seeedstudio.com/recamera_develop_with_node-red/
```

### 选项 2：reCamera 显微镜套件

#### 步骤 1：设备准备与组装

确保你的设备清单包含以下物品：

- reCamera POE 设备

- 3D 打印机（2 台）

- M12 镜头（2 个）

- M12 镜头延长支架（3 个）

- 显微镜支架

- 12V 电源适配器

- Type-C 数据线

![img](/img/reCamera_retrain/image-20250916181501348.png)

**按图所示组装支架，连接 12V 电源，并安装 3D 打印部件。**

![image-20250916181710125](/img/reCamera_retrain/image-20250916181710125.png)

如图所示，显微镜套件包含两个镜头，需取下广角镜头，更换为另外两个专用镜头。

![image-20250916181945699](/img/reCamera_retrain/image-20250916181945699.png)

#### 步骤 2：镜头使用与部署

##### 子选项 1：镜头 1（显微镜镜头）的使用

按图所示，取下原有镜头，安装三个镜头延长转接器，然后装上镜头 1。

![image-20250916182307461](/img/reCamera_retrain/image-20250916182307461.png)

如图所示，**使用 USB 数据线连接电脑**。

访问 [192.168.42.1](http://192.168.42.1) 进入 reCamera 的加载页面，登录的 **用户名** 为：root；**密码** 为：recamera.1

![image-20250916182526749](/img/reCamera_retrain/image-20250916182526749.png)

若出现如下提示，请点击 “取消”。

![image-20250916173741710](/img/reCamera_retrain/image-20250916173741710.png)

可向工作人员领取标本切片，将其放置在载物台上。通过调整相机与物体的位置，即可观察微观世界的图像。

![image-20250916174515742](/img/reCamera_retrain/image-20250916174515742.png)

##### 子选项 2：镜头 2（PCB 显微镜头）的使用

按图所示，取下原有镜头，安装一个镜头延长转接器，然后装上镜头 2。

![image-20250916183322865](/img/reCamera_retrain/image-20250916183322865.png)

如图所示，**使用 USB 数据线连接电脑**。

访问 [192.168.42.1](http://192.168.42.1) 进入 reCamera 的加载页面，登录的 **用户名** 为：root；**密码** 为：recamera.1

![image-20250916183224595](/img/reCamera_retrain/image-20250916183224595.png)

若出现如下提示，请点击 “取消”。

![image-20250916173741710](/img/reCamera_retrain/image-20250916173741710.png)

通过调整相机与物体的位置，即可观察 PCB（印制电路板）的细节。

![image-20250916174804728](/img/reCamera_retrain/image-20250916174804728.png)

模型部署操作，请参考 [将](#Deploy-the-model-to-reCamera)[模型](#Deploy-the-model-to-reCamera)[部署到 r](#步骤 2：将模型部署到 reCamera)[eCame](#步骤 2：将模型部署到 reCamera)[ra](#Deploy-the-model-to-reCamera)。

### 选项 3：reCamera 高清 POE 版本

#### 步骤 1：准备与连接

- 确保你已准备好 reCamera 高清 POE 设备和一根 Type-C 数据线。

- ![image-20250917100232816](/img/reCamera_retrain/\image-20250917100232816.png)使用数据线将 reCamera 与电脑连接。

#### ![image-20250916150122897](/img/reCamera_retrain/\image-20250916150122897.png)步骤 2：查看预览

- 访问 [192.168.42.1](http://192.168.42.1) 进入 reCamera 的加载页面，登录的 **用户名** 为：root；**密码** 为：recamera.1

- 模型部署或登录操作，请参考 [将模型部署](#步骤 2：将模型部署到 reCamera)[到 reC](#Deploy-the-model-to-reCamera)[amera](#步骤 2：将模型部署到 reCamera)。

- reCamera 高清 POE 版的软件与默认版一致，但该版本设备配备了更清晰的 CMOS 传感器和更广角度的镜头。

![image-20250917101250846](/img/reCamera_retrain/image-20250917101250846.png)

> 步骤 3：POE 硬件新增功能说明（可选）

**以下介绍 POE 设备硬件的新增功能，该内容与本次工作坊无关，仅供参考。**

POE 版设备支持 POE 供电功能，可通过 POE 交换机的网线实现供电。

![image-20250917102116142](/img/reCamera_retrain/image-20250917102116142.png)

你可以通过路由器分配的 IP 地址访问设备后端。

![image-20250917102737014](/img/reCamera_retrain/image-20250917102737014.png)

若无法查看路由器分配的 IP 地址，也可通过 USB 数据线连接设备，访问 [该地](http://192.168.42.1/#/network)[址](http://192.168.42.1/#/network) 查看 IPv4 地址。

![image-20250917103057065](/img/reCamera_retrain/image-20250917103057065.png)

POE 版设备还配备 3 个自定义 IO 端口和 1 个串口：

- 490、487、488 均可配置为输入或输出 IO 端口，这些数字代表芯片内部的配置编号。

![image-20250917103312258](/img/reCamera_retrain/image-20250917103312258.png)

### 选项 4：reCamera 云台版本

#### 步骤 1：准备与连接

- 确保你已准备好 reCamera 云台版设备、一个 12V 直流电源适配器和一根 Type-C 数据线。

- ![image-20250917105125132](/img/reCamera_retrain/\image-20250917105125132.png)首先，将 12V 电源适配器连接到 reCamera 云台，等待云台电机完成校准。

（**注意：必须先将 12V 电源适配器连接到云台，否则电机无法成功初始化。**）

- ![image-20250917105338466](/img/reCamera_retrain/\image-20250917105338466.png)然后，使用 USB 数据线将 reCamera 云台与电脑连接。

#### ![image-20250916150122897](/img/reCamera_retrain/\image-20250916150122897.png)步骤 2：查看预览

- 访问 [192.168.42.1](http://192.168.42.1) 进入 reCamera 的加载页面，登录的 **用户名** 为：root；**密码** 为：recamera.1

- 模型部署或登录操作，请参考 [将模型部署到 reCamera](#步骤 2：将模型部署到 reCamera)。

- reCamera 云台版的预览功能与默认版类似，但额外增加了电机控制和目标跟踪功能。

**![image-20250917110022368](/img/reCamera_retrain/image-20250917110022368.png)

## 注意事项

若连接 reCamera 后电脑无法正常联网，可参考以下 wiki 文档获取解决方案：

[常见问题](https://wiki.seeedstudio.com/cn/recamera_network_connection/)

