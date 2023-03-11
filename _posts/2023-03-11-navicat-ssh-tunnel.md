---
layout: post
title: navicat通过ssh跳板机登陆报错Disconnected:No supported authentication methods available (server send:publickey)
author: "gebaocai"
header-style: text
lang: zh
tags: [navicat]
---

这个错误是新版本openssh的特性，rsa类型密钥不在当前公钥识别类型中，导致无法连接。执行命令，然后重启sshd

```
echo "PubkeyAcceptedKeyTypes=+ssh-rsa" >>/etc/ssh/sshd_config 
service sshd restart
```