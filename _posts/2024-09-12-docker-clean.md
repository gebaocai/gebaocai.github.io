---
layout: post
title: Docker磁盘清理
author: "gebaocai"
header-style: text
lang: zh
tags: [docker]
---

## 介绍
当我们使用docker时，经常会创建新镜像和新容器，不清理无用的镜像。时间久了，硬盘就会满了。

## 清理docker空间，通过prune

### 命令查看磁盘使用情况。
```
du -hs /var/lib/docker/ 
```

### 清理docker磁盘
```
#类似于Linux上的df命令，用于查看Docker的磁盘使用情况:
docker system df

#可以用于清理磁盘，删除关闭的容器、无用的数据卷和网络，以及dangling镜像(即无tag的镜像)。 
docker system prune

#清理得更加彻底，可以将没有容器使用Docker镜像都删掉。注意，这两个命令会把你暂时关闭的容器，以及暂时没有用到的Docker镜像都删掉了…
#所以使用之前一定要想清楚.。我没用过，因为会清理 没有开启的  Docker 镜像。
docker system prune -a

#命令可以进一步查看空间占用细节，以确定是哪个镜像、容器或本地卷占用过高空间
docker system df -v 

#删除悬空的镜像。
docker image prune

#删除无用的容器
docker container prune

#删除无用的卷
docker volume prune

#删除无用的网络
docker volume prune
```

### 手动清除
```
对于悬空镜像和未使用镜像可以使用手动进行个别删除：
1、删除所有悬空镜像，不删除未使用镜像：
docker rmi $(docker images -f "dangling=true" -q)

2、删除所有未使用镜像和悬空镜像
docker rmi $(docker images -q)

3、清理卷
如果卷占用空间过高，可以清除一些不使用的卷，包括一些未被任何容器调用的卷（-v 详细信息中若显示 LINKS = 0，则是未被调用）：
删除所有未被容器引用的卷：
docker volume rm $(docker volume ls -qf dangling=true)

4、容器清理
如果发现是容器占用过高的空间，可以手动删除一些：
删除所有已退出的容器：
docker rm -v $(docker ps -aq -f status=exited)
删除所有状态为dead的容器
docker rm -v $(docker ps -aq -f status=dead)
```

## 迁移 /var/lib/docker 目录
/var/lib/docker/overlay2 占用很大，清理Docker占用的磁盘空间，迁移 /var/lib/docker 目录。

### 停止docker服务。
```
systemctl stop docker
```

### 创建新的docker目录，执行命令df -h,找一个大的磁盘。 我在 /home目录下面建了 /home/docker/lib目录，执行的命令是：

```
mkdir -p /home/docker/lib
```

### 迁移/var/lib/docker目录下面的文件到 /home/docker/lib

```
rsync -avz /var/lib/docker /home/docker/lib/
```

### 配置 /etc/systemd/system/docker.service.d/devicemapper.conf。查看 devicemapper.conf 是否存在。如果不存在，就新建。

```
sudo mkdir -p /etc/systemd/system/docker.service.d/

sudo vi /etc/systemd/system/docker.service.d/devicemapper.conf
```

### 然后在 devicemapper.conf 写入：（同步的时候把父文件夹一并同步过来，实际上的目录应在 /home/docker/lib/docker ）

```
[Service]

ExecStart=

ExecStart=/usr/bin/dockerd  --graph=/home/docker/lib/docker
```

### 重新加载 docker
```
systemctl daemon-reload
systemctl restart docker
```

### 确定容器没问题后删除/var/lib/docker/目录中的文件。

```
rm -rf /var/lib/docker/*
```
