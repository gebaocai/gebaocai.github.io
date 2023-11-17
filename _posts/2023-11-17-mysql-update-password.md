---
layout: post
title: 解决MySQL不需要密码就能登录问题
author: "gebaocai"
header-style: text
lang: zh
tags: [MySql]
---

前言
------
因为执行了一个更改数据库root用户密码的命令，当我更改完后，发现用我新密码和旧密码都能登陆，于是感觉没有输密码，直接回车就能登录，而我在配置中也没有进行免密码登陆的操作，最后，执行了一条命令解决update user set plugin = "mysql_native_password";
修改密码及解决无密码登陆问题都在下面命令中：

```
use mysql;
 
#有password字段的版本,版本<5.7的，执行这个命令
update user set password=password('你的密码') where user='root'; 
 
#无password字段的版本,也就是版本=>5.7的，执行这个命令
update user set authentication_string=password("你的密码") where user='root';  
 
update user set plugin="mysql_native_password"; 
 
flush privileges;
 
exit;
```