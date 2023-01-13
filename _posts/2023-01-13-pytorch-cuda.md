---
layout: post
title: PyTorch CUDA windows10 1050ti环境安装
author: "gebaocai"
header-style: text
lang: zh
tags: [PyTorch, CUDA, AI]
---
我本机是windows10+GeForce GTX 1050 Ti的环境, 配置了下pytorch环境， 这里整理一下.

升级显卡驱动程序
------
去Nvidia官网地址 https://developer.nvidia.com/cuda-downloads 按系统版本，选择下载在线升级包, 下载完后，选择**自定义**先升级显卡驱动,如图:
![](/img/in-post/2023/pytorch-cuda/cuda-driver.png)

我的已经升级完成， 升级完重启

安装cuda
------

按下图选择， 下一步，完成安装

![](/img/in-post/2023/pytorch-cuda/cuda.png)

检查cuda版本
------

```
nvidia-smi
```
![](/img/in-post/2023/pytorch-cuda/nvidia-smi.png)

可以看到当前CUDA Version已经是12.0了

安装pytorch
------
通过conda创建一个python环境
```
conda create -n python3.8 python=3.8.15
activate python3.8
```

安装pytorch，我们到 https://pytorch.org/get-started/locally/ 选择安装方式
![](/img/in-post/2023/pytorch-cuda/pytorch-install.png)

这里我们采用pip安装(用conda安装一直识别不到cuda)

```
pip install torch torchvision torchaudio --trusted-host=download.pytorch.org --extra-index-url https://download.pytorch.org/whl/cu117 
```

验证是否安装成功
------
```
import torch
torch.cuda.is_available()
```
![](/img/in-post/2023/pytorch-cuda/cuda-is-available.png)

显示`True`就证明pytorch cuda已经配置成功了。

其它
------
最开始使用的conda安装pytorch， 走了很多弯路，不过发现了mamba这个东西， 下载和检查本地依赖比conda快很多, 这里也记录一下, mamba是conda包管理器的c++实现, 下面是mamba的几个描述:
* 使用多线程并行下载、管理包文件
* libsolv 用于更快地解决依赖关系，这是 Red Hat、Fedora 和 OpenSUSE 的 RPM 包管理器中使用的最先进的库
* mamba 的核心部分是用 C++ 实现的，以实现最高效率

mamba使用方式和conda几乎一样
![](/img/in-post/2023/pytorch-cuda/mamba.png)

以cuda包为例:
```
mamba search cuda
mamba install cuda
mamba remove cuda
```
