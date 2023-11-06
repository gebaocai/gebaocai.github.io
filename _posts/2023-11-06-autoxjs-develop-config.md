---
layout: post
title: Autoxjs-开发环境搭建
author: "gebaocai"
header-style: text
lang: zh
tags: [Autoxjs]
---

前言
------
一个支持无障碍服务的Android平台上的JavaScript 运行环境 和 开发环境，其发展目标是类似JsBox和Workflow。

环境配置
------

#### 电脑端:
1. 在电脑端是基于 vscode 开发的。
2. 在 vscode 中安装扩展 ***Auto.js-Autox.js-VSCodeExt***

### Android：
[https://github.com/kkevsekk1/AutoX/releases](https://github.com/kkevsekk1/AutoX/releases)  
如果下载过慢可以右键复制 Release Assets 中APK文件的链接地址，粘贴到 [http://toolwa.com/github/](http://toolwa.com/github/) 等github加速网站下载

#### APK版本说明：
- universal: 通用版（不在乎安装包大小/懒得选就用这个版本，包含以下2种CPU架构so）
- armeabi-v7a: 32位ARM设备（备用机首选）
- arm64-v8a: 64位ARM设备（主流旗舰机）

找到适合自己的apk, 安装到手机里。

### 调试
1. 确认安卓和 PC 在同一个网段
手机和电脑连同一路由器，如果是模拟器就选桥接网络。
2. 开启调试服务
在 vscode 中，帮助 → 显示所有命令，也可以使用快捷键 ctrl + shift + p

然后在输入框中输入 autojs，选择 开启服务，如图（常用的也就前面几个）
![](/img/in-post/2023/autoxjs_develop/vscode_autoxjs.png)

3. 手机连接
手机打开安装好的 Autox ，左上角按钮调出菜单，先开启无障碍服务，然后点连接电脑。如果正常，vscode 会提示 New device attached: xxx

4. 运行脚本
回到 vscode，创建一个 js 文件（名字随意），写入测试代码
```
toast("autoxjs test")
```
右上角执行， 手机会有汽包消息

5. 停止脚本、停止服务
和第二步开启调试服务一样，ctrl + shift + p