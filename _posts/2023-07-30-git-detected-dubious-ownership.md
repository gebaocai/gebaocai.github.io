---
layout: post
title: git fatal detected dubious ownership
author: "gebaocai"
header-style: text
lang: zh
tags: [git]
---
重新安装了系统， 就遇到这个错误了。 
原因就是重新安装系统后，文件所属的用户变了导致的。（即使是同样的用户名，重装系统后，用户ID也不一样）

解决方法
------
1. 右键单击 仓库 文件夹，属性，安全性，高级。
2. 单击所有者行上的“更改”
3. 查找您的用户（高级...，立即查找，选择您的用户）。确认
4. 在更改屏幕上，启用“替换子容器和对象的所有者”。
