---
layout: post
title: Git忽略已经提交过的文件
author: "gebaocai"
header-style: text
lang: zh
tags: [Git]
---

Git忽略文件分为几种情况：

从未提交过的文件
------
也就是添加之后从来没有提交（commit）过的文件，可以使用.gitignore忽略该文件

该文件只能作用于未跟踪的文件（Untracked Files），也就是那些从来没有被 git 记录过的文件

比如，忽略log/下的日志文件，可以在.gitignore中写

```
log/*
```

已经推送（push）过的文件
------

想从git远程库中删除，并在以后的提交中忽略，但是却还想在本地保留这个文件
```
git rm --cached .vscode/tasks.json
```
后面的 .vscode/tasks.json 是要从远程库中删除的文件的路径，支持通配符*

比如，不小心提交到git上的一些log日志文件，想从远程库删除，可以用这命令。

已经推送（push）过的文件，想在以后的提交时忽略此文件
------
即使本地已经修改过，而且不删除git远程库中相应文件

```
git update-index --assume-unchanged .vscode/tasks.json
```
后面的 .vscode/tasks.json 是要忽略的文件的路径。如果要忽略一个目录，打开 git bash，cd 到 目标目录下，执行：
```
git update-index --assume-unchanged $(git ls-files | tr '\n' ' ')
```
比如有一个配置文件 jdbc.properties 记录数据库的链接信息，每个人的链接信息肯定不一样，但是又要提供一个标准的模板，用来告知如何填写链接信息，那么就需要在git远程库上有一个标准配置文件，然后每个人根据自己的具体情况，修改一份链接信息自用，而且不会将该配置文件提交到库！