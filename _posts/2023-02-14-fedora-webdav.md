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

安装davfs2
------
```
sudo dnf install davfs2
```

挂载小雅
------

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

开机自动挂载小雅
------

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