---
layout: post
title: Failed to switch root: Specified switch root path '/sysroot' does not seem to be an 0S tree, os-release file is missing 
author: "gebaocai"
header-style: text
lang: zh
tags: [Linux]
---
重新安装了系统， 基于Hyper-V导入Centos, 结果报以下错误

Failed to switch root: Specified switch root path '/sysroot' does not seem to be an 0S tree, os-release file is missing 

找出错误信息
------
cat /run/initramfs/rdsosreport.txt

```
Serverl.localdomain systemdtll: Reached target Switch Root,
Serverl.localdomain systemdtll: Starting Switch Root...
Serverl.localdomain systemctlt6551: Failed to switch root: Specified switch root path '/sysroot' does not seem to be an 0S tree, os-release file is missing 
Serverl.localdomain systemdtll: initrd-switch-root.service: Main process exited, code =exited, status=l/FAILURE
Serverl.localdomain systemdtll: initrd-switch-root.service: Failed with result 'exit- code '.
Serverl.localdomain systemdtll: Failed to start Switch Root.
Serverl.localdomain systemdtll: Startup finished in 1.388s (kernel) + 0 (initrd) + 1. 825s (userspace) = 3.214s.
Serverl.localdomain systemdtll: initrd-switch-root.service: Triggering OnFailure= dep endencies.
Serverl.localdomain systemdtll: Starting Setup Uirtual Console... t 3.2760981 Serverl.localdomain systemdtll: Started Setup Uirtual Console, 
Serverl.localdomain systemdtll: Started Emergency Shell,
Serverl.localdomain systemdtll: Reached target Emergency Mode,
Serverl.localdomain systemdtll: Received SIGRTT1IN+21 from PID 421 (plymouthd). t 134.5871651 Serverl.localdomain kernel: random: crng init done
Serverl.localdomain kernel: random: 7 urandom warning(s) missed due to ratelimiting
```


解决方法
------

```
mount -o remount,rw /sysroot
cp /usr/lib/os-release /sysroot/etc/
cd /
exit
```

经测试， 通过以上方法能成功进入系统。

