---
layout: post
title: fedora安装之后的配置
author: "gebaocai"
header-style: text
lang: zh
tags: [Fedora]
---

保持系统更新
------
sudo dnf update
sudo dnf upgrade

修改DNF配置，提升速度：
------
https://dnf.readthedocs.io/en/latest/...

```
sudo vi /etc/dnf/dnf.conf
#添加以下内容:
fastestmirror=True
max_parallel_downloads=10
defaultyes=True
keepcache=True
```

软件源配置
------
#### 添加[RPM Fusion](https://rpmfusion.org/Configuration)
------

```
#添加 free 和 nonfree 仓库
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm

# Install ApStream metadata
sudo dnf groupupdate core
```
#### 添加[FLATPACK](https://flatpak.org/setup/Fedora)软件包

```
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

使用[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/fedora/)替换官方软件源
------
Fedora 默认使用 Metalink 给出推荐的镜像列表，保证用户使用的镜像仓库足够新，并且能够尽快拿到安全更新，从而提供更好的安全性。所以通常情况下使用默认配置即可，无需更改配置文件。

由于 Metalink 需要从国外的 Fedora 项目服务器上获取元信息，所以对于校园内网、无国外访问等特殊情况，metalink 并不适用，此时可以如下修改配置文件。

Fedora 的软件源配置文件可以有多个，其中： 系统默认的 fedora 仓库配置文件为 /etc/yum.repos.d/fedora.repo，系统默认的 updates 仓库配置文件为 /etc/yum.repos.d/fedora-updates.repo 。将上述两个文件先做个备份，根据 Fedora 系统版本分别替换为下面内容，之后通过 sudo dnf makecache 命令更新本地缓存，即可使用 TUNA 的软件源镜像。

``fedora`` 仓库 (/etc/yum.repos.d/fedora.repo)
```
[fedora]
name=Fedora $releasever - $basearch
failovermethod=priority
baseurl=https://mirrors.tuna.tsinghua.edu.cn/fedora/releases/$releasever/Everything/$basearch/os/
metadata_expire=28d
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-$releasever-$basearch
skip_if_unavailable=False
```
``updates`` 仓库 (/etc/yum.repos.d/fedora-updates.repo)
```
[updates]
name=Fedora $releasever - $basearch - Updates
failovermethod=priority
baseurl=https://mirrors.tuna.tsinghua.edu.cn/fedora/updates/$releasever/Everything/$basearch/
enabled=1
gpgcheck=1
metadata_expire=6h
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-$releasever-$basearch
skip_if_unavailable=False
```

``fedora-modular`` 仓库 (/etc/yum.repos.d/fedora-modular.repo)
```
[fedora-modular]
name=Fedora Modular $releasever - $basearch
failovermethod=priority
baseurl=https://mirrors.tuna.tsinghua.edu.cn/fedora/releases/$releasever/Modular/$basearch/os/
enabled=1
metadata_expire=7d
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-$releasever-$basearch
skip_if_unavailable=False
```

``updates-modular`` 仓库 (/etc/yum.repos.d/fedora-updates-modular.repo)
```
[updates-modular]
name=Fedora Modular $releasever - $basearch - Updates
failovermethod=priority
baseurl=https://mirrors.tuna.tsinghua.edu.cn/fedora/updates/$releasever/Modular/$basearch/
enabled=1
gpgcheck=1
metadata_expire=6h
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-fedora-$releasever-$basearch
skip_if_unavailable=False
```

***更新本地缓存***
```
sudo dnf makecache
```

安装[NVIDIA显卡驱动](https://rpmfusion.org/Howto/NVIDIA)
------
#### 1.查看显卡类型
```
/sbin/lspci | grep -e VGA
```
#### 2.安装显卡驱动
```
sudo dnf update -y # and reboot if you are not on the latest kernel
sudo dnf install akmod-nvidia # rhel/centos users can use kmod-nvidia instead
sudo dnf install xorg-x11-drv-nvidia-cuda #optional for cuda/nvdec/nvenc support
```

桌面外观Gnome Tweak Tool
------
一款对gnome界面調整的软件，可以修改主题、字体、gnome扩展、窗口、开机自启动等
```
sudo dnf install gnome-tweak-tool
# 安装浏览器gnome扩展组件
sudo dnf install chrome-gnome-shell
# 安装菜单编辑器
sudo dnf install menulibre
```
通过上面安装浏览器插件之后，我们可以访问[gnome扩展](https://extensions.gnome.org/)来安装所需要的插件。

安装chrome
------
```
# 启用chrome仓库
sudo dnf config-manager --set-enabled google-chrome

# 安装
sudo dnf install google-chrome-stable
```
***注:启用chrome仓库后，也可以在Fedora系统里的Software商店里安装Chrome***

安装vscode
------

```
# We currently ship the stable 64-bit VS Code in a yum repository, the following script will install the key and repository:
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc
sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'

#Then update the package cache and install the package using dnf (Fedora 22 and above):
dnf check-update
sudo dnf install code
```

***注意:用snap安装vscode， 在vscode里不能输入中文。***


安装中文输入法
------
```
udo dnf install fcitx5 kcm-fcitx5 fcitx5-chinese-addons fcitx5-table-extra fcitx5-zhuyin fcitx5-configtool
```

***注意，严格安装上述package，之前想当然安装了fcitx5，安装的不全导致不支持。***

此时，在一个终端手动执行fcitx5

```
fcitx5
```

在另一个终端，配置fcitx5

```
fcitx5-config-qt
```

选中中文输入法pinyin。如下图:
![](/img/in-post/2023/fedora-config/select-input.png)


在其它窗口，按Ctrl+space，就可以切换到中文输入法了。

开机启动fcitx5
------

#### 设置默认输入法
```
sudo alternatives --config xinputrc # choose fcitx5.

cat << EOF > ~/.config/environment.d/00-fcitx5.conf
INPUT_METHOD=fcitx5
GTK_IM_MODULE=fcitx5
QT_IM_MODULE=fcitx5
XMODIFIERS=@im=fcitx5
EOF
```

#### 添加自动启动

```
ln -s /usr/share/applications/org.fcitx.Fcitx5.desktop ~/.config/autostart/
```

#### 重新登陆
```
logout
```

安装docker
------

#### 删除旧的版本
```
sudo dnf remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```

#### 设置docker仓库
```
sudo dnf -y install dnf-plugins-core
sudo dnf config-manager \
    --add-repo \
    https://download.docker.com/linux/fedora/docker-ce.repo
```

#### 安装Docker Engine
```
sudo dnf install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

#### 启动Docker.
```
sudo systemctl start docker
```

#### 建立 docker 用户组
默认情况下，docker 命令会使用 `Unix socket` 与 `Docker` 引擎通讯。而只有 root 用户和 docker 组的用户才可以访问 Docker 引擎的 Unix socket。出于安全考虑，一般 Linux 系统上不会直接使用 root 用户。因此，更好地做法是将需要使用 docker 的用户加入 docker 用户组。

建立 docker 组：
```
sudo groupadd docker
```

将当前用户加入 docker 组：
```
sudo usermod -aG docker $USER
```

***退出当前终端并重新登录，进行如下测试。***

#### 测试Docker是否安装成功
```
docker run hello-world
```

#### 安装Docker Compose
Linux 上我们可以从 Github 上下载它的二进制包来使用，最新发行的版本地址：https://github.com/docker/compose/releases。
运行以下命令以下载 Docker Compose 的当前稳定版本：

```
sudo curl -L "https://github.com/docker/compose/releases/download/v2.15.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

>Docker Compose 存放在 GitHub，不太稳定。
>
>你可以也通过执行下面的命令，高速安装 Docker Compose。
```
curl -L https://get.daocloud.io/docker/compose/releases/download/v2.15.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```

***要安装其他版本的 Compose，请替换 v2.15.1。***

将可执行权限应用于二进制文件：
```
sudo chmod +x /usr/local/bin/docker-compose
```

创建软链：
```
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

测试是否安装成功：
```
docker-compose version
```

#### 卸载Docker Compose
```
sudo rm /usr/local/bin/docker-compose
```