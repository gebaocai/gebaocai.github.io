---
layout: post
title: Linux/fedora下安装MiniConda详细步骤
author: "gebaocai"
header-style: text
lang: zh
tags: [Fedora, MiniConda]
---

下载MiniConda安装文件
------
在[conda](https://docs.conda.io/en/latest/miniconda.html#linux-installers)下载页里下载`Linux 64-bit`文件, 我这里采用python 3.10版本的，下载地址为``https://repo.anaconda.com/miniconda/Miniconda3-py310_22.11.1-1-Linux-x86_64.sh``.

![](/img/in-post/2023/fedora-miniconda/miniconda-installer.png)

验证安装文件的完整系
------
```
sha256sum Miniconda3-py310_22.11.1-1-Linux-x86_64.sh
#输出
00938c3534750a0e4069499baf8f4e6dc1c2e471c86a59caa0dd03f4a9269db6
```

安装
------
```
bash Miniconda3-latest-Linux-x86_64.sh
```

根据提示安装
------
一直``yes``就可以

验证
------
重新打开终端, 输入```python```如图:

![](/img/in-post/2023/fedora-miniconda/python-terminal.png)

到此Miniconda安装成功。