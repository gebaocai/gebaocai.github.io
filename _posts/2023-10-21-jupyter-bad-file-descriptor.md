---
layout: post
title: jupyter notebook运行出现Bad file descriptor (bundled\zeromq\src\epoll.cpp:100)错误，避坑指南
author: "gebaocai"
header-style: text
lang: zh
tags: [jupyter, python]
---

jupyter notebook运行出现Bad file descriptor (bundled\zeromq\src\epoll.cpp:100)， 莫慌，下面告诉你方法。

用Anaconda或pycharm运行jupyter notebook时候，创建ipynb文件没一会儿就开始报错,而且在nb上没法运行代码，最后才知道是我的windows用户是中文名导致的。

唉，所以创建Win10/11用户名一定要用英文！！！

------

#### 启用Administrator账户

Win10/11默认将Administrator账户禁用了，我们在这里需启用Administrator账户，方法如下：
以管理员身份运行CMD——> 输入
```net user administrator /active:yes```

#### 切换账户
1. 先注销当前账户：Windows+X——>注销；
2. 再重启电脑（这步可解决用Administrator账户登陆后用户文件夹显示被占用而无法修改的问题）；
![](/img/in-post/2023/jupyter_badfile_descriptor/file_in_use.png)
3. 然后切换到Administrator账户登录。

#### 重命名用户文件夹
C盘——>用户，找到用中文用户名命名的文件夹，重命名为英文。

#### 修改注册表
1. 打开Windows注册表：Windows+R——>运行——>输入 regedit；
2. 依次展开HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Profilelist，在Profilelist下的文件夹对应系统中用户，而文件夹中ProfileImagePath值是指向每个用户文件夹的地址，一个一个点击查看，找到中文名用户的对应所在的ProfileImagePath值。
3. 修改ProfileImagePath的值，将地址改为修改成英文的文件夹名（注：与C盘的文件夹名一致）。
![](/img/in-post/2023/jupyter_badfile_descriptor/regedit.png)
编辑用户名
![](/img/in-post/2023/jupyter_badfile_descriptor/change_user_name.png)
4. 再次注销，切换账户登录，就可以看到用户文件夹名和CMD已更改。

最后，若觉得多个账户看着不习惯的话，可以以管理员身份打开CMD，输入```net user administrator /active:no```，就可关闭administrator账户了。

那么，jupyter notebook就可以正常运行。
![](/img/in-post/2023/jupyter_badfile_descriptor/jupyter.png)

***最后一点，环境变量中，如果含有那个中文用户名的话，需要自行修改。***
