---
layout: post
title: Linux/fedora下安装IntelliJ IDEA
author: "gebaocai"
header-style: text
lang: zh
tags: [Fedora, IntelliJ IDEA]
---

安装OpenJDK
------
```
sudo dnf install java-1.8.0-openjdk.x86_64
```

安装IntelliJ IDEA 2021.2
------

#### 下载IntelliJ IDEA 2021.2.4 
`https://download.jetbrains.com/idea/ideaIU-2021.2.4.tar.gz` 

#### 解压到要安装的目录
```
sudo tar -xzf ideaIU-*.tar.gz -C /opt
```

在`/opt`解压的目录里运行`idea.sh`启动IntelliJ IDEA

#### 创建桌面图标(下面任选一个)
* On the Welcome screen, click Configure
* From the main menu, click Tools

安装ide-eval-resetter
------
#### 项目介绍
ide-eval-resetter可以重置`IntelliJ IDEA` 30天试用时间。

最新的IDE试用需要登录，我们可以任选以下方式中的一种来继续使用重置插件：
* 使用网络上[热心大佬](https://jetbra.in/s)收集总结的key，进入IDE后使用重置插件。
* 登录账号试用IDE，安装设置好本插件，退出登录账号重启IDE即可。
* 先安装旧版本IDE，安装设置好本插件，升级IDE到最新版本即可。

***不管哪种方法原理都是为了让你进入IDE，以便重置插件接管试用。***

#### 安装
* 点击这个[链接(v2.3.5)](https://plugins.zhile.io/files/ide-eval-resetter-2.3.5-c80a1d.zip)下载插件的zip包
* 通常可以直接把zip包拖进IDE的窗口来进行插件的安装。如果无法拖动安装，你可以在Settings/Preferences... -> Plugins 里手动安装插件（Install Plugin From Disk...）
* 插件会提示安装成功。

#### 如何使用
* 一般来说，在IDE窗口切出去或切回来时（窗口失去/得到焦点）会触发事件，检测是否长时间（25天）没有重置，给通知让你选择。（初次安装因为无法获取上次重置时间，会直接给予提示）
* 也可以手动唤出插件的主界面：
    * 如果IDE没有打开项目，在Welcome界面点击菜单：Get Help -> Eval Reset
    * 如果IDE打开了项目，点击菜单：Help -> Eval Reset
* 唤出的插件主界面中包含了一些显示信息，2个按钮，1个勾选项：
    * 按钮：Reload 用来刷新界面上的显示信息。
    * 按钮：Reset 点击会询问是否重置试用信息并重启IDE。选择Yes则执行重置操作并重启IDE生效，选择No则什么也不做。（此为手动重置方式）
    * 勾选项：Auto reset before per restart 如果勾选了，则自勾选后每次重启/退出IDE时会自动重置试用信息，你无需做额外的事情。（此为自动重置方式）