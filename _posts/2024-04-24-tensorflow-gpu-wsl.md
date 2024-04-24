---
layout: post
title: Windows安装TensorFlow GPU
author: "gebaocai"
header-style: text
lang: zh
tags: [TensorFlow]
---

前言
------
在tensorflow 2.10之后，您无法在Windows操作系统上使用tensorflow-gpu，因此您需要在Window 10或Window 11上使用WSL来创建conda环境以使用GPU运行tensorflow。

安装 WSL2
------
```
wsl --install Ubuntu-20.04
```

等待安装完毕后，会让输入账号和密码，按提示操作就行

进入wsl系统后， 更新下
```
sudo apt-get update
sudo apt-get upgrade
```

安装 Miniconda
------
这四个命令快速、安静地安装最新的 64 位版本的安装程序，然后自行清理。 要为 Linux 安装不同版本或体系结构的 Miniconda，请在 wget 命令中更改 .sh 安装程序的名称。

```
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm -rf ~/miniconda3/miniconda.sh
```

安装后，初始化新安装的 Miniconda。 以下命令针对 bash 和 zsh shell 进行初始化：
```
~/miniconda3/bin/conda init bash
~/miniconda3/bin/conda init zsh
```

创建虚拟环境
```
conda create --name tf python=3.9
conda activate tf
```

安装 TensorFlow-GPU
------

在上面创建好的虚拟环境里执行安装命令
```
python3 -m pip install tensorflow[and-cuda]
```


***您不需要在系统上安装 cuda 或 cudnn。 仅使用 $ pip install tensorflow[and-cuda] 安装的 cuda 库就足够了。***


编辑 *~/.bashrc* 配置安装好的cuda库
```
NVIDIA_PACKAGE_DIR="~/miniconda3/envs/tf/lib/python3.9/site-packages/nvidia"

for dir in $NVIDIA_PACKAGE_DIR/*; do
    if [ -d "$dir/lib" ]; then
        export LD_LIBRARY_PATH="$dir/lib:$LD_LIBRARY_PATH"
    fi
done
```

当前窗口生效
```
source ~/.bashrc
```

测试是否成功

```
python -c "import tensorflow as tf;print(tf.config.list_physical_devices('GPU') )"
```

![](/img/in-post/2024/tensorflow-gpu-wsl/tf-gpu-show-ok.png)

配置vscode
------

Windows下， VScode安装*wsl*插件

![](/img/in-post/2024/tensorflow-gpu-wsl/vscode-install-wsl.png)

安装*python*插件
![](/img/in-post/2024/tensorflow-gpu-wsl/vscode-install-python.png)
***windows下安装完python也要单独安装给wsl安装***

安装完后， 选择*Connect to Wsl*后， 选择工程目录

![](/img/in-post/2024/tensorflow-gpu-wsl/vscode-open-folder.png)

简单编写代码， 测试一下:

![](/img/in-post/2024/tensorflow-gpu-wsl/vscode-gpu-show-ok.png)

大功告成~