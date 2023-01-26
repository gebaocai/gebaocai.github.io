---
layout: post
title: fedora安装之后的配置
author: "gebaocai"
header-style: text
lang: zh
tags: [PyTorch, CUDA, AI]
---

[main]
; 是否开启gpg校验
gpgcheck=1
; 允许保留多少旧内核包
installonly_limit=3
; 删除软件同时删除依赖包
clean_requirements_on_remove=True
; 查找最快镜像
fastestmirror=true
; 下载增量包
deltarpm=true
; 最大并发下载数量
max_parallel_downloads=6

1. 保持系统更新
sudo dnf update
sudo dnf upgrade

2. DNF配置，提升速度：
https://dnf.readthedocs.io/en/latest/...

sudo nano /etc/dnf/dnf.conf
添加以下内容:
fastestmirror=True
max_parallel_downloads=10
defaultyes=True
keepcache=True

3. 启用RPM Fusion
https://rpmfusion.org/Configuration

```
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm

sudo dnf groupupdate core
```


4. FLATPACK软件包
https://flatpak.org/setup/Fedora

flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flat...

5. 安装NVIDIA显卡驱动：
https://rpmfusion.org/Howto/NVIDIA

6. 桌面外观Gnome Tweak Tool：
sudo dnf install gnome-tweak-tool

7. 安装GNOME extention:
extensions.gnome.org
dnf install chrome-gnome-shell gnome-extensions-app

8. 安装Timeshift backup:
sudo dnf install timeshift

9. vscode

```
# We currently ship the stable 64-bit VS Code in a yum repository, the following script will install the key and repository:
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'

#Then update the package cache and install the package using dnf (Fedora 22 and above):
dnf check-update
sudo dnf install code
```
Visual Studio Code is officially distributed as a Snap package in the Snap Store:

```
sudo snap install --classic code # or code-insiders
```

Once installed, the Snap daemon will take care of automatically updating VS Code in the background. You will get an in-product update notification whenever a new update is available.

Installing snap on Fedora
```
sudo dnf install snapd
sudo ln -s /var/lib/snapd/snap /snap
```

#输入法
```
udo dnf install fcitx5 kcm-fcitx5 fcitx5-chinese-addons fcitx5-table-extra fcitx5-zhuyin fcitx5-configtool
```

注意，严格安装上述package，之前想当然安装了fcitx5，安装的不全导致不支持。



此时，在一个终端手动执行fcitx5

$ fcitx5



在另一个终端，配置fcitx5

$ fcitx5-config-qt

选中中文输入法pinyin。



在其它窗口，按Ctrl+space，就可以切换到中文输入法了。

开机启动fcitx5
1 设置默认输入法

$ sudo alternatives --config xinputrc


There are 2 programs which provide 'xinputrc'.

  Selection    Command
-----------------------------------------------
*  1           /etc/X11/xinit/xinput.d/ibus.conf
 + 2           /etc/X11/xinit/xinput.d/fcitx5.conf

Enter to keep the current selection[+], or type selection number: 2



2 添加自动启动

```
ln -s /usr/share/applications/org.fcitx.Fcitx5.desktop ~/.config/autostart/
```



3 设置环境变量

不设置好像不影响，但是可能某些程序需要这些环境变量。


```
$ cat << EOF > ~/.config/environment.d/00fcitx5.conf

INPUT_METHOD=fcitx5
GTK_IM_MODULE=fcitx5
QT_IM_MODULE=fcitx5
XMODIFIERS=@im=fcitx5

EOF
```