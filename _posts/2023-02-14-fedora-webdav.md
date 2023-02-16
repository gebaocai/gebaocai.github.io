---
layout: post
title: Linux/fedora下通过WebDav挂载小雅
author: "gebaocai"
header-style: text
lang: zh
tags: [Linux, WebDav]
---

最近发现了一个不错的网盘项目， 把很多资源放到阿里云盘了
![](/img/in-post/2023/fedora-webdav/xiaoya.png)
地址在这里 [小雅](http://alist.xiaoya.pro/)

现在想把小雅通过webdav的方式挂在到fedora的系统下

安装小雅
------
```
一键安装和更新容器
curl -s http://docker.xiaoya.pro/update_xiaoya.sh | bash

端口：5678
访问： http://xxxxx:5678/ （xxxx 为你alist所在设备的 IP）

webdav 账号密码
用户: guest 密码: guest_Api789

重启就会自动更新数据库及搜索索引文件
docker restart xiaoya
```
~~davfs2~~
------

#### 安装davfs2

```
sudo dnf install davfs2
```

#### 挂载小雅

* 创建挂在目录
```
sudo mkdir -m 755 /mnt/xiaoya
```

* 挂载
```
sudo mount -t davfs -o noexec http://xxxxx:5678/dav /mnt/xiaoya
```
按提示输入`Username`和`Password`， 两个分别是`guest`和`guest_Api789`
* 查看、确认已挂载成功
```
df -h /mnt/xiaoya
#输出
Filesystem                    Size  Used Avail Use% Mounted on
http://192.168.0.99:5678/dav  1.3T  763G  509G  61% /mnt/xiaoya
```

* 卸载
```
umount /mnt/xiaoya
```

注：出现umount target is busy
可以通过lsof查找占用进程

```
sudo lsof /mnt/xiaoya
COMMAND      PID     USER   FD   TYPE DEVICE SIZE/OFF           NODE NAME
updatedb  512750     root    6r   DIR  0,330      792              1 /mnt/xiaoya
updatedb  512750     root    7r   DIR  0,330        0 94620245402672 /mnt/xiaoya/整理中/xxT影视资源/美剧/B（33部）/b 布里奇顿/布里奇顿S01

sudo kill 512750
```

#### 开机自动挂载小雅


* 修改/etc/fstab
在/etc/fstab添加以下记录

```
http://192.168.0.99:5678/dav  /mnt/xiaoya  davfs   noauto,user   0   0
```

* 修改/etc/davfs2/secrets

在/etc/davfs2/secrets里添加主机、用户名、密码
```
http://192.168.0.99:5678/dav	guest	guest_Api789
```

* /etc/rc.d/rc.local
设置开机启动
```
mount /mnt/xiaoya
```

rclone
------

#### 安装 
```
sudo dnf install rclone
```

#### 配置rclone
要配置 WebDAV 远程，您需要有一个 URL，以及一个用户名和密码。如果您知道您正在连接的是哪种系统，那么 rclone 可以启用额外的功能。 

这是一个如何制作一个名为 remote 的遥控器的示例。第一次运行：
```
rclone config
```

这将引导您完成交互式设置过程：

```
No remotes found, make a new one?
n) New remote
s) Set configuration password
q) Quit config
n/s/q> n
name> remote
Type of storage to configure.
Choose a number from below, or type in your own value
[snip]
XX / WebDAV
   \ "webdav"
[snip]
Storage> webdav
URL of http host to connect to
Choose a number from below, or type in your own value
 1 / Connect to example.com
   \ "https://example.com"
url> http://192.168.0.99:5678/dav
Name of the WebDAV site/service/software you are using
Choose a number from below, or type in your own value
...
44 / Union merges the contents of several upstream fs
   \ (union)
45 / Uptobox
   \ (uptobox)
46 / WebDAV
   \ (webdav)
...
vendor> 46
User name
user> guest
Password.
y) Yes type in my own password
g) Generate random password
n) No leave this optional password blank
y/g/n> y
Enter the password:
password:
Confirm the password:
password:
Bearer token instead of user/pass (e.g. a Macaroon)
bearer_token>
Remote config
--------------------
[remote]
name = xiaoya
type = webdav
url = http://192.168.0.99:5678/dav
....
--------------------
y) Yes this is OK
e) Edit this remote
d) Delete this remote
y/e/d> y
```

配置完成后，您就可以像这样使用 rclone， 在 WebDAV 的顶层列出目录
```
rclone lsd remote:
```
列出 WebDAV 中的所有文件
```
rclone ls remote:
```

将本地目录复制到名为 backup 的 WebDAV 目录
```
rclone copy /home/source remote:backup
```

#### 挂载小雅
```
rclone mount Alist: /home/gebaocai/xiaoya --use-mmap --umask 000 --allow-other --allow-non-empty --dir-cache-time 24h --cache-dir=/home/gebaocai/cache --vfs-cache-mode full --buffer-size 512M --vfs-read-chunk-size 16M --vfs-read-chunk-size-limit 64M --vfs-cache-max-size 10G --daemon
```

#### 卸载
```
umount /home/gebaocai/xiaoya
```