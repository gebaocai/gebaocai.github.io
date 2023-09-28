---
layout: post
title: 安卓安装系统证书，破解古古识字
author: "gebaocai"
header-style: text
lang: zh
tags: [安卓, 古古识字]
---

古古识字解锁界面
------
![gugushizi](/img/in-post/2023/android-root-certificate/gugushizi.PNG)

### 实现思路

#### Fiddler模拟登录返回接口
返回格式：
```
POST https://www.gugushizi.com/api/sendaction
200 OK with automatic headers (text/plain)

Response:
{"status":"true","errorCode":"getPayProject获取成功：0000000000","phone":"0000000000","pay_project":"11009","pay_time":"2023-9-28 21:19:39"}
```
**11009代表永久**


安卓安装系统证书
------

#### 导出证书(以Fiddler Classic为例)

Tools->Options->HTTPS
![Fiddler-Root-Certificate](/img/in-post/2023/android-root-certificate/fiddler-certificate.PNG)

#### 计算证书的HASH值

```
//以下根据导出的证书格式2选1
//.cer格式证书
openssl x509 -inform DER -subject_hash_old -in FiddlerRoot.cer
//.pem证书
openssl x509 -inform DER -subject_hash_old -in 证书.pem
```
#### 计算结果
![hash](/img/in-post/2023/android-root-certificate/hash.PNG)

#### 生成系统系统预设格式证书文件
```
//cer格式
openssl x509 -inform DER -text -in xxx.cer > 269953fb.0
//pem格式
openssl x509 -inform PEM -text -in xxx.pem > 269953fb.0
```

**注意： 证书格式为{hash}.0**

#### 最后编辑一下输出的文件，把 -----BEGIN CERTIFICATE----- 到最后的这部分移动到开头。结果如下
![certificate](/img/in-post/2023/android-root-certificate/certificate.PNG)

#### 上传证书到Android手机

```
adb push 269953fb.0 /sdcard
adb shell
su
mount -o remount,rw /system
cp /sdcard/269953fb.0 /system/etc/security/cacerts/
chmod 644 /system/etc/security/cacerts/269953fb.0
```

到此然后重启手机。就可以正常抓https数据包了

吐槽一下华为
------
手里有好几部华为手机， 解锁Bootloader都不太友好， 无非两种方法：
1. 拆机解锁（PotatoNV）(免费)

2. 第三方软解(dcunlocker， hcu client...) (收费)

感觉手里这部mate20是最后一部华为手机了。