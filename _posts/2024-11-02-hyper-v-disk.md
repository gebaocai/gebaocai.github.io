---
layout: post
title: Hyper-v给Linux虚拟机扩展硬盘空间
author: "gebaocai"
header-style: text
lang: zh
tags: [hyper-v]
---

## 遇到问题
在Hyper-V中，给虚拟机扩展硬盘空间可不是一件简单地在设置里调数字的事儿。实际操作起来，坑还是不少的。我最近就踩了几个，所以赶紧记录下来，也希望能帮到其他遇到同样问题的朋友。

首先，咱们在Hyper-V管理器里增加虚拟硬盘的大小很容易，图形界面点点点就搞定了。

## Hyper-V管理器操作如下：
在hyper-v管理器，右键虚拟机
![](/img/in-post/2024/hyper-v/1.png)
选择硬盘驱动器，再点编辑
![](/img/in-post/2024/hyper-v/2.png)
选择扩展
![](/img/in-post/2024/hyper-v/3.png)
选择配置磁盘， 输入新大小后， 点击完成。
![](/img/in-post/2024/hyper-v/4.png)

但是！这只是第一步。虚拟机内部的操作系统并不知道硬盘变大了，它看到的还是原来的小硬盘。所以，接下来才是关键。

## 虚拟机操作：
如果是Windows系统，那就相对简单些。磁盘管理工具里，找到扩展后的硬盘，右键“扩展卷”，一路下一步，基本就搞定了。不过，有时候会碰到“扩展卷”是灰色的情况。这时候别慌，可能是分区格式不对，MBR格式的磁盘最多只能支持2TB，超过了就没办法扩展。转换成GPT格式就能解决这个问题了。转换格式可以用Diskpart命令行工具，不过操作要小心，数据无价！

Linux系统稍微麻烦一点儿, 

1. 首先输入lsblk查看当前逻辑卷和磁盘空间
```
lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda               8:0    0   50G  0 disk 
├─sda1            8:1    0    1G  0 part /boot
└─sda2            8:2    0   19G  0 part 
  ├─cl_192-root 253:0    0   17G  0 lvm  /
  └─cl_192-swap 253:1    0    2G  0 lvm  [SWAP]
sr0              11:0    1 1024M  0 rom
```

2. 通过发现虚拟机已经识别到扩展后的硬盘了， 现在需要调整分区并释放空间给sda2。执行命令
```
parted /dev/sda resizepart 2 100%
```
使用 parted 重新调整分区大小, 这里 2 是 /dev/sda2 的分区编号，100% 表示扩展到整个磁盘的可用空间。
3. 检查调整后的分区

```
parted /dev/sda print
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 53.7GB
Sector size (logical/physical): 512B/4096B
Partition Table: msdos
Disk Flags: 
Number  Start   End     Size    Type     File system  Flags
 1      1049kB  1075MB  1074MB  primary  xfs          boot
 2      1075MB  53.7GB  52.6GB  primary               lvm
```

发现分区已经调整

4. 运行 pvresize
调整完分区后，执行以下命令来扩展物理卷：
```
pvresize /dev/sda2
```

5. 扩展逻辑卷
```
lvextend -l +100%FREE /dev/mapper/cl_192-root
```
逻辑卷信息可以通过fdisk -l查看
6. 调整文件系统大小
你需要调整文件系统大小以匹配新的逻辑卷大小。根据你的文件系统类型，使用以下命令

* xfs(hyper-v模式下)

```
xfs_growfs /dev/mapper/cl_192-root
```
* ext2/ext3/ext4

```
resize2fs /dev/mapper/cl_192-root
```

7. 检查是否扩容成功
```
df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 3.8G     0  3.8G   0% /dev
tmpfs                    3.8G     0  3.8G   0% /dev/shm
tmpfs                    3.8G   18M  3.8G   1% /run
tmpfs                    3.8G     0  3.8G   0% /sys/fs/cgroup
/dev/mapper/cl_192-root   47G   17G   31G  35% /
/dev/sda1               1014M  339M  676M  34% /boot
```

## 总结
总而言之，扩展Hyper-V虚拟机硬盘空间，看似简单，实则暗藏玄机。Windows系统相对友好，图形界面操作为主，但要注意MBR和GPT格式的限制。Linux系统则需要更专业的命令行操作，考验你的功底。记住，操作前一定要备份数据，以防万一！