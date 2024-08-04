---
formatterOff: "@formatter:off"
title: Git本地分支修剪（删除）
subtitle: Git本地分支修剪（删除）
summary: Git本地分支修剪（删除）
typed:
  - "git branch -vv " 
categories: [git]
tags: [git] 
mark:
  pin: false
  hot: false
date: 2024-08-04 08:04:20 +800 
version: 1.0
formatterOn: "@formatter:on"
---

# Git本地分支修剪(删除)

> 你是否也有这样的问题，在项目中从远程拉取的分支，远程分支已经删除了，但仍然存在于本地仓库中，你是否也曾想找到一个`git`命令来删除这些在远程已经不存在的本地分支呢？

假设你的项目有许多在本地计算机上创建的分支，但在远程仓库中不存在。你可以轻松删除与远程仓库不同步的所有本地分支，但在此之前，你可能需要检查本地计算机中可用的所有分支，你可以运行 `git branch`。

现在，要列出所有远程分支，你可以使用 `git branch -r` 命令。要在一个命令中实现这两个结果，你可以使用 `git branch -a` 命令。确认分支后，你可以继续阅读本文的其余部分。

## 基本用法

### 删除不在远程仓库的跟踪分支

你可以轻松运行以下命令来修剪不在远程仓库上的跟踪分支。该命令会修剪不在远程仓库上的**跟踪分支**，但是**本地分支尚未删除**。

```shell
git remote prune origin
```



### 删除不在远程仓库的本地分支

要实际删除本地分支，你可能需要执行以下说明的额外步骤。

列出所有具有详细输出的分支，

```shell
git branch -vv
```

现在，你用管道输出到 `grep` 查找 `origin/.* : gone]`，因为 `gone` 状态被置于远程仓库中不可用但在本地计算机中可用的分支。

```shell
grep 'origin/.*: gone]'
```

再次将输出传送到 awk（这是一个非常好的格式化工具），如下所示。

```shell
awk '{print $1}'
```

最后，你希望将输出通过管道传输到 `xargs`，当你需要从一个命令获取输出并将其用作另一个命令的参数时，可以使用它。你不要将步骤 2 的输出传递给 `git branch -D` 命令来删除本地分支，如下所示。

```shell
xargs git branch -D
```

### 小结

因此，修剪和删除远程仓库中不可用的所有本地分支的最后两个线性命令如下。

```shell
git remote prune origin
```

运行上面的命令后，你可能想运行下面的命令来实现删除远程仓库中不可用的本地分支。

```shell
git branch -vv | grep 'origin/.*: gone]' | awk '{print $1}' | xargs git branch -D
```

## 高阶用法

你可以将上述两个线性命令通过别名alias的方式添加到自定义快捷指令中，以`zsh`为例，在`.zshrc`文件中追加以下内容后，就可以通过`gbd`来快速修剪分支了。

```text
## 删除远程已经不存在的本地分支
alias gbd="git remote prune origin && git branch -vv | grep 'origin/.*: gone]' | awk '{print $1}' | xargs git branch -D;"
```