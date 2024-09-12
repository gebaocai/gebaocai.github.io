---
layout: post
title: Docker容器日志查看与清理
author: "gebaocai"
header-style: text
lang: zh
tags: [docker]
---

## 介绍
当我们使用docker时，经常会创建新镜像和新容器，容器运行久了也会产生大量的日志。时间久了，硬盘就会满了。

## 日志清理

### 找出Docker容器日志
在linux上，容器日志一般存放在/var/lib/docker/containers/container_id/下面， 以json.log结尾的文件（业务日志）很大，查看各个日志文件大小的脚本docker_log_size.sh，内容如下：
```
#!/bin/sh

echo "======== docker containers logs file size ========"  

logs=$(find /var/lib/docker/containers/ -name *-json.log)  

for log in $logs  
    do  
        ls -lh $log   
    done  
```
然后执行下面命令
```
chmod +x docker_log_size.sh
./docker_log_size.sh
```
### 清理Docker容器日志
如果docker容器正在运行，那么使用rm -rf方式删除日志后，通过df -h会发现磁盘空间并没有释放。原因是在Linux或者Unix系统中，通过rm -rf或者文件管理器删除文件，将会从文件系统的目录结构上解除链接（unlink）。如果文件是被打开的（有一个进程正在使用），那么进程将仍然可以读取该文件，磁盘空间也一直被占用。正确姿势是cat /dev/null > *-json.log，当然你也可以通过rm -rf删除后重启docker。接下来，提供一个日志清理脚本clean_docker_log.sh，内容如下：

```
#!/bin/sh 
  
echo "======== start clean docker containers logs ========"  
  
logs=$(find /var/lib/docker/containers/ -name *-json.log)  
  
for log in $logs  
    do  
        echo "clean logs : $log"  
        cat /dev/null > $log  
    done  

echo "======== end clean docker containers logs ========"  
```
然后执行下面命令
```
chmod +x docker_log_size.sh
./clean_docker_log.sh
```

### 设置Docker容器日志大小
- 设置一个容器服务的日志大小上限
上述方法，日志文件迟早又会涨回来。要从根本上解决问题，需要限制容器服务的日志大小上限。这个通过配置容器docker-compose的max-size选项来实现
```
nginx: 
  image: nginx:1.12.1 
  restart: always
  logging: 
    driver: "json-file"
    options: 
      max-size: "100m" 
```
重启nginx容器之后，其日志文件的大小就被限制在100m，再也不用担心了。

- 全局设置
新建/etc/docker/daemon.json，若有就不用新建了。添加log-dirver和log-opts参数，样例如下
```
{
  "log-driver":"json-file",
  "log-opts": {"max-size":"100m", "max-file":"3"}
}
```
max-size=500m，意味着一个容器日志大小上限是500M，
max-file=3，意味着一个容器有三个日志，分别是id+.json、id+1.json、id+2.json。

> **注意：设置的日志大小，只对新建的容器有效。**

```
// 重启docker守护进程

# systemctl daemon-reload

# systemctl restart docker
```