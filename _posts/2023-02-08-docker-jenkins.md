---
layout: post
title: docker-compose安装jenkins
author: "gebaocai"
header-style: text
lang: zh
tags: [Jenkins, Docker]
---

> 我使用的是Docker-in-Docker pipeline模式，也就是agent为docker。如果不是这种就看[官方](https://www.jenkins.io/zh/doc/)就行了.

创建jenkins工作目录
mkdir -p data/jenkins

编写docker-compose.yaml文件
------

```
version: '3.3'
services:
  jenkins:
    image: 'jenkins/jenkins:lts'
    container_name: jenkins
    restart: always
    networks:
      macvlan-net:
        ipv4_address: 192.168.0.50
    environment:
      HTTP_PROXY: 'http://192.168.0.54:7890'
      HTTPS_PROXY: 'http://192.168.0.54:7890'
      NO_PROXY: 'localhost, *.test.lan'
    volumes:
      - ./data/jenkins:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
      - $HOME:/home
```

`networks`和`environment`如果没需求可以不设置， 分别配置的是静态ip和代理。

启动jenkis
------
`docker-compose up -d jenkins`

查看密码并登陆
------
`docker logs -f jenkins`

启动之后，会默认生成一个密码, 用做登录用.
![](/img/in-post/2023/docker-in-docker-jenkins/setup-jenkins-02-copying-initial-admin-password.png)

登录地址为：`http://192.168.0.50:8080`
> 我是在docker-compose配置的静态ip, 默认因该为`http://127.0.0.1:8080`

***做到这里，Docker运行JenKins就完成了***

docker not found!!!
* Enter the jenkins container as root user:
`docker exec -it -u0 <container id> bash`
* Install docker
`curl https://get.docker.com > dockerinstall && chmod 777 dockerinstall && ./dockerinstall`
* And then exit the Jenkins container and change docker.sock privilege to read and write for all users with:
`sudo chmod 666 /var/run/docker.sock`

Jenkins error buildind : docker: /lib/x86_64-linux-gnu/libc.so.6: version `glibc_2.34' not found (required by docker)