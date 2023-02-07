---
layout: post
title: Docker的网络代理配置
author: "gebaocai"
header-style: text
lang: zh
tags: [Docker]
---

在国内用docker去拉取image时会很慢， 此时可以配置镜像加速器。国内很多云服务商都提供了国内加速器服务，例如：

*  [阿里云加速器(点击管理控制台 -> 登录账号(淘宝账号) -> 右侧镜像工具 -> 镜像加速器 -> 复制加速器地址)](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)
* 网易云加速器 https://hub-mirror.c.163.com
* 百度云加速器 https://mirror.baidubce.com

配置加速器:

`vim /etc/docker/daemon.json`
```
{
  "registry-mirrors": [
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com"
  ]
}
```
> 注意，一定要保证该文件符合 json 规范，否则 Docker 将不能启动。

之后重新启动服务。

```
sudo systemctl daemon-reload
sudo systemctl restart docker
```

然而，在公司或者某些资源必须使用代理的怎么办？Docker的代理配置，略显复杂，因为有三种场景。 但基本原理都是一致的，都是利用Linux的`http_proxy`等环境变量。

dockerd代理
------

在执行docker pull时，是由守护进程dockerd来执行。 因此，代理需要配在dockerd的环境中。 而这个环境，则是受systemd所管控，因此实际是systemd的配置。

`sudo mkdir -p /etc/systemd/system/docker.service.d`

`sudo touch /etc/systemd/system/docker.service.d/proxy.conf`

在这个proxy.conf文件（可以是任意*.conf的形式）中，添加以下内容：

```
[Service]
Environment="HTTP_PROXY=http://192.168.0.54:7890"
Environment="HTTPS_PROXY=http://192.168.0.54:7890"
Environment="NO_PROXY=localhost,127.0.0.1,.example.com"
```
> 我的代理服务器是192.168.0.54:7890，这里需要按自己的代理修改

Container代理
------
#### 用户级代理
`vim ~/.docker/config.json`
```
{
 "proxies":
 {
   "default":
   {
     "httpProxy": "http://192.168.0.54:7890",
     "httpsProxy": "http://192.168.0.54:7890",
     "noProxy": "localhost,127.0.0.1,.example.com"
   }
 }
}
```
这种方法默认在所有配置修改后启动的容器生效。
#### 容器级代理
容器的网络代理，也可以直接在其运行时通过-e注入http_proxy等环境变量。docker-compose的是要配置`environment` 格式如下:
```
web:
    environment:
      HTTP_PROXY: 'http://192.168.0.54:7890'
      HTTPS_PROXY: 'http://192.168.0.54:7890'
      NO_PROXY: 'localhost, *.test.lan'
```

docker build代理
------
虽然docker build的本质，也是启动一个容器，但是环境会略有不同，用户级配置无效。 在构建时，需要注入http_proxy等参数。
```
docker build . \
    --build-arg "HTTP_PROXY=http://192.168.0.54:7890" \
    --build-arg "HTTPS_PROXY=http://192.168.0.54:7890" \
    --build-arg "NO_PROXY=localhost,127.0.0.1,.example.com" \
    -t your/image:tag
```