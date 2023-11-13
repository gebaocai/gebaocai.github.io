---
layout: post
title: pyinstaller打包exe文件
author: "gebaocai"
header-style: text
lang: zh
tags: [python, pyinstaller]
---

前言
------
PyInstaller 用于将 Python 代码打包成适用于各种操作系统的独立可执行应用程序。它采用 Python 脚本并生成一个包含所有必要依赖项的单个可执行文件，并且可以在未安装 Python 的计算机上运行。这允许轻松分发和部署 Python 应用程序，因为用户无需在其系统上安装 Python 和任何必需的模块即可运行该应用程序。此外，PyInstaller 还可用于创建单文件可执行文件，这些文件是包含应用程序所有必需依赖项的单个可执行文件。这可以使分发应用程序变得更加容易，因为用户只需要下载一个文件。

如何安装 PyInstaller
------

```
pip install pyinstaller
```

### 升级pyinstaller

```
pip install --upgrade pyinstaller
```

如何使用 PyInstaller 创建 EXE
------

#### 常用参数
```
-h 查看帮助
-w 忽略控制台，打包gui软件时使用
-F dist目录中只生成一个exe文件
-p 表示你自己定义需要加载的类库的路径
-D 创建dist目录，里面包含exe以及其他一些依赖性文件（默认，可不添加）
-i 指定打包程序使用的图标文件
```

#### 命令使用

```
pyinstaller -i ico.png -F -w demo.py
```


#### 两种打包方式

1. 文件夹模式onedir
```
pyinstaller fileren.py
```

执行完命令后，在项目文件夹下多出了三个文件，build，dist和fileren.spec、__pycache__。
- build文件夹用于存储日志文件。
- dist文件夹储存可执行文件即相关依赖。
- __pycache__文件夹里是Python版本信息。
- fileren.spec打包的配置文件，可以配置依赖资源。

这种模式下，需要把整个dist文件夹发给别人才能运行。

2. 单文件模式onefile

加上-F参数，全部的依赖文件都会被打包到exe文件中，在dist文件夹中只有一个可执行文件，

把这个可执行文件发给别人就可以直接运行了。

```
pyinstaller -w -F fileren.py
```

注意事项
------
有时候，除了代码本身，还包括一些外部资源文件，如图片、配置文件等。可以修改第一次打包完成的配置文件XXX.spec配置文件，然后执行命令pyinstaller xxx.spec，便可按照spec文件中的新配置重新打包。
binaries元组，二进制文件（如.exe/.dll/.so等），比如binaries=[('ci64.dll','.'),('ABDLL64.dll','.')]

datas元组，非二进制文件（如图片文件、文本文件等），例如：datas=[('icons','icons’)]