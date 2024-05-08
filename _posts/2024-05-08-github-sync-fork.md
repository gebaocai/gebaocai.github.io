---
layout: post
title: github sync fork conflicts
author: "gebaocai"
header-style: text
lang: zh
tags: [github]
---

前言
------
在使用 GitHub 进行协作开发时，经常会遇到需要同步 fork 仓库与上游仓库（upstream）的情况。如果在同步过程中遇到冲突，但又不想发起`pull request`, 可以按照以下步骤解决：
![](/img/in-post/2024/github-sync-fork/sync-fork-conflicts.png)

解决步骤
------

1、
配置远程存储库

检查仓库是否有`remote upstream repository `
```
$ git remote -v
> origin  https://github.com/YOUR-USERNAME/YOUR-FORK.git (fetch)
> origin  https://github.com/YOUR-USERNAME/YOUR-FORK.git (push)
```

如果如上图没有`remote upstream repository `就按如下命令配置下
```
git remote add upstream https://github.com/ORIGINAL-OWNER/ORIGINAL-REPOSITORY.git

```

配置完就会如下图一样
```
$ git remote -v
> origin    https://github.com/YOUR-USERNAME/YOUR-FORK.git (fetch)
> origin    https://github.com/YOUR-USERNAME/YOUR-FORK.git (push)
> upstream  https://github.com/ORIGINAL-OWNER/ORIGINAL-REPOSITORY.git (fetch)
> upstream  https://github.com/ORIGINAL-OWNER/ORIGINAL-REPOSITORY.git (push)
```

2、
获取上游仓库的最新提交
```
git fetch upstream
```

3、
将上游仓库的`master`分支合并到你的本地`master`分支
```
git checkout master # 假设你的本地分支名为 master
git merge upstream/master
```

4、
解决冲突

如果在合并或变基过程中遇到冲突，Git 会暂停并告诉你哪些文件有冲突。你需要手动解决这些冲突。打开冲突的文件，你会看到类似以下的标记：
```
<<<<<<< HEAD
// 你的分支上的代码
=======
// 上游仓库的代码
>>>>>>> upstream/master
```
你需要编辑这些文件，删除这些标记，并决定保留哪些代码。解决冲突后，保存文件并退出编辑器。

提交信息并推送至远程仓库
```
git commit -m 'resolve conflicts'
git push origin master
```