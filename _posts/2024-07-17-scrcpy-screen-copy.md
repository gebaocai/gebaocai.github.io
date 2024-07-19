---
layout: post
title: 使用scrcpy将手机无线投屏到电脑
author: "gebaocai"
header-style: text
lang: zh
tags: [scrcpy]
---

介绍
------
在现代生活中，智能手机和电脑已经成为我们日常生活中不可或缺的工具。有时，我们需要将手机屏幕投射到电脑上，例如在进行演示、玩游戏或查看手机内容时。scrcpy是一款开源工具，可以帮助我们轻松地实现这一目标。本文将介绍如何使用scrcpy将手机无线投屏到电脑。
![](/img/in-post/2024/scrcpy-screen-copy/scrcpy.png)

准备工作
------
在开始投屏之前，我们需要进行一些准备工作。首先，确保你的电脑和手机都连接到同一个Wi-Fi网络。其次，下载并安装scrcpy, 可以从[github](https://github.com/Genymobile/scrcpy/releases) 下载最新的 scrcpy包。 此外，你还需要在手机上启用USB调试模式，这可以在开发者选项中找到。最后，确保你的手机和电脑上都安装了ADB（Android Debug Bridge）工具，这是scrcpy运行所必需的。


初次连接
------
初次连接时，我们需要通过USB线将手机连接到电脑。打开命令行或终端窗口，输入scrcpy命令，这时scrcpy会自动检测到你的手机并开始投屏。一旦成功连接，你会看到手机屏幕显示在电脑上。此时，你可以断开USB连接，并在命令行中输入scrcpy --tcpip以启用无线投屏功能。接下来，拔掉USB线，输入scrcpy -s {手机IP地址}，这样就可以实现无线投屏了。（有无线调试的手机， 直接打开就无需USB线了）

高级设置
------
crcpy不仅仅提供了基本的投屏功能，它还支持多种高级设置。例如，你可以调节分辨率以适应不同的使用场景。通过命令行参数，你可以设置比默认更高或更低的分辨率。此外，scrcpy还支持屏幕录制功能，你可以在投屏的同时录制手机屏幕内容。只需在命令行中添加--record {文件名}.mp4参数即可开始录制。

* Capture the screen in H.265 (better quality), limit the size to 1920, limit the frame rate to 60fps, disable audio, and control the device by simulating a physical keyboard:
```
scrcpy --video-codec=h265 --max-size=1920 --max-fps=60 --no-audio --keyboard=uhid
scrcpy --video-codec=h265 -m1920 --max-fps=60 --no-audio -K  # short version
```

* Record the device camera in H.265 at 1920x1080 (and microphone) to an MP4 file:
```
scrcpy --video-source=camera --video-codec=h265 --camera-size=1920x1080 --record=file.mp4
```

* Capture the device front camera and expose it as a webcam on the computer (on Linux):
```
scrcpy --video-source=camera --camera-size=1920x1080 --camera-facing=front --v4l2-sink=/dev/video2 --no-playback
```

* Control the device without mirroring by simulating a physical keyboard and mouse (USB debugging not required):
```
scrcpy --otg
```

* 常用命令
```
scrcpy --no-audio --disable-screensaver --turn-screen-off
```

结论
------
使用scrcpy将手机无线投屏到电脑是一种高效、便捷的方法。通过简单的设置和命令操作，你可以轻松地实现这一功能。无论是用于演示、游戏还是其他用途，scrcpy都是一个值得推荐的工具。希望本文能帮助你更好地理解和使用scrcpy进行无线投屏。