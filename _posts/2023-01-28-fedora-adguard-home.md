---
layout: post
title: Linux/fedora下安装AdGuardHome详细步骤
author: "gebaocai"
header-style: text
lang: zh
tags: [Fedora, AdGuardHome]
---

安装AdGuardHome
------
[官方](https://github.com/AdguardTeam/AdGuardHome#getting-started)提供了几种方法，这里我们采用`snap`方式安装.

```
#install snapd
sudo dnf install snapd
sudo ln -s /var/lib/snapd/snap /snap
#install adguard-home
sudo snap install adguard-home
```

安装完成后，在浏览器中打开 `http://127.0.0.1:3000/` 进行初始设置，选择监听网卡时选择`所有接口`,若出现`53`端口占用情况，关闭本机域名解析服务.
```
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
```

配置AdGuardHome
------

#### 在`设置-DNS设置`里， 
* 上游 DNS 服务器修改为
```https://dns.alidns.com/dns-query
https://doh.pub/dns-query
```
* Bootstrap DNS 服务器修改为
```
114.114.114.114
```
* 点***应用***保存修改

* DNS 服务配置如下图
![](/img/in-post/2023/fedora-adguard-home/dns-service-config.png)

点***保存***存储

#### 在`过滤器-DNS 拦截列表`里， 
选择左下脚`添加阻止列表-从列表选择`中，选中CHN的过滤规则

![](/img/in-post/2023/fedora-adguard-home/ad-filter.png)

点`保存`，等待片刻就可以了。

在设备上使用 AdGuard Home DNS
------
* 更改路由器 DNS 地址
* 更改手机 DNS 地址
* 更改电脑 DNS 地址

常见问题
------
* 安装时通常会出现 53（本地 DNS 服务器） 端口冲突的问题

    * 关闭本机域名解析服务
        ```
        sudo systemctl stop systemd-resolved
        sudo systemctl disable systemd-resolved
        ```

* 设置完DNS后， 在AdguardHome后台并没有看到请求

    * 确认AdguardHome开启
    * 检查Linux防火墙是否是开启的
        ```
        systemctl status firewalld
        systemctl stop firewalld
        systemctl disable firewalld
        ```
        我直接把防火墙关闭了，有公网地址的服务器请勿模仿~
    
