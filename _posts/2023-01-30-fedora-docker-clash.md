---
layout: post
title: Docker Clash Premium 旁路由网关模式透明代理
author: "gebaocai"
header-style: text
lang: zh
tags: [Docker, Clash Premium]
---

把家里闲置的笔记本当旁路由使用， 今天用Clash Premium的Docker模式实现一遍。

设置macvlan网络
------
用macvlan模式给clash的容器分配一个独立ip, 这样就可以与宿主机分离出来。

创建macvlan网名为macvlan-net 。将subnet，gateway和parent值修改为在您的环境中有意义的值。

网卡查看方式可能通过`ip addr`查看
![](/img/in-post/2023/fedora-docker-clash/ipaddr.png)

```
docker network create -d macvlan \
  --subnet=192.168.0.0/24 \
  --gateway=192.168.0.1 \
  -o parent=enp4s0 \
  macvlan-net
```
用Docker-compose部署容器
------
`vim docker-compose.yml` 输入以下内容
```
version: '3.3'
services:
  clash:
    container_name: clash
    image: dreamacro/clash-premium
    restart: always
    privileged: true
    devices:
      - /dev/net/tun
    volumes:
      - ./conf/clash:/root/.config/clash
      - ./data/clash/clash-dashboard/dist:/root/ui
    networks:
      macvlan-net:
        ipv4_address: 192.168.0.54
networks:
  macvlan-net:
    external: true
```

可以看到volumes挂载了2个目录：
* ./conf/clash

    这里是clash的配置文件， 我们可以直接把[`clash_for_windows`](https://gebaocai.github.io/2023/01/26/fedora-clash-for-windows/)的配置文件拿过来用, 路径在`~/.config/clash/profiles/`

    ![](/img/in-post/2023/fedora-docker-clash/lsprofiles.png)

    把需要的文件拷贝到./conf/clash路径下
    ```
    cp ~/.config/clash/Country.mmdb ./conf/clash
    cp ~/.config/clash/profiles/1674919537204.yml ./conf/clash/config.yaml
    ```

    编辑下./conf/clash/config.yaml, 按如下代码进行修改, ***proxies里的数据不要动***.
    ```
    mixed-port: 7890
    allow-lan: true
    bind-address: '*'
    mode: rule
    log-level: info
    external-controller: '0.0.0.0:9090'
    external-ui: /root/ui
    dns:
        enable: true
        ipv6: false
        default-nameserver: [223.5.5.5, 119.29.29.29]
        enhanced-mode: fake-ip
        fake-ip-range: 198.18.0.1/16
        use-hosts: true
        nameserver: ['https://doh.pub/dns-query', 'https://dns.alidns.com/dns-query']
        fallback: ['https://doh.dns.sb/dns-query', 'https://dns.cloudflare.com/dns-query', 'https://dns.twnic.tw/dns-query', 'tls://8.8.4.4:853']
        fallback-filter: { geoip: true, ipcidr: [240.0.0.0/4, 0.0.0.0/32] }
    tun:
        enable: true
        stack: system
        auto-route: true
        auto-detect-interface: true
        dns-hijack:
            - any:53
            - tcp://any:53
    auto-redir:
        enable: true
        auto-route: true
    ```
* ./data/clash/clash-dashboard/dist

    这里是clash的web控制台, 切到`./data/clash`目录下，执行以下代码
    ```
    git clone git@github.com:Dreamacro/clash-dashboard.git
    cd clash-dashboard
    sudo dnf install npm
    npm config set registry https://registry.npmjs.org
    sudo npm install -g pnpm
    pnpm install
    pnpm build
    ```
启动docker clash
```
docker-compose up clash
```

访问`http://192.168.0.54:9090/ui`简单配置下就可以正常使用了
![](/img/in-post/2023/fedora-docker-clash/clashui.png)

设置路由器的默认网关
------
登录路由器，在DHCP服务器里，把默认网关改为`192.168.0.54`。
![](/img/in-post/2023/fedora-docker-clash/router-dhcp-config.png)

这样连接到路由器的设备都可以通过Clash上网了。

常见问题
------
一般在macvlan模式下同网段的其他机器可以和容器互通，但宿主不能和容器互通，这是在macvlan模式设计的时候为了安全而禁止了宿主机和容器直接通信。
如果想要实现互通，有个曲线救国的方法，就是macvlan与macvlan之间可以互通，只需要在宿主机再创建一个macvlan网络，然后修改路由，让数据经过这个macvlan达到互通的目的。
#### 增加rc.local配置文件
`vim /etc/rc.d/rc.local`
```
#!/bin/bash
ip link add macvlan2 link enp4s0 type macvlan mode bridge
ip addr add 192.168.0.55 dev macvlan2
ip link set macvlan2 up

ip route add 192.168.0.54 dev macvlan2
```
#### 在rc-local.service中添加Install字段
`vim /lib/systemd/system/rc-local.service`
```
[Unit]
Description=/etc/rc.d/rc.local Compatibility
Documentation=man:systemd-rc-local-generator(8)
ConditionFileIsExecutable=/etc/rc.d/rc.local
After=network.target

[Service]
Type=forking
ExecStart=/etc/rc.d/rc.local start
TimeoutSec=0
RemainAfterExit=yes
GuessMainPID=no

[Install]
WantedBy=multi-user.target
```
#### 授权文件并启动设置开机自启
```
chmod +x /etc/rc.d/rc.local
systemctl enable rc-local.service
systemctl start rc-local.service
systemctl status rc-local.service
```
#### 测试
```
(base) [gebaocai@fedora-ge ~]$ ping 192.168.0.54
PING 192.168.0.54 (192.168.0.54) 56(84) bytes of data.
64 bytes from 192.168.0.54: icmp_seq=1 ttl=64 time=0.290 ms
64 bytes from 192.168.0.54: icmp_seq=2 ttl=64 time=0.110 ms
```
