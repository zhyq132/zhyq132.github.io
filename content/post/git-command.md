---
title: "Git Command"
date: 2017-02-24T14:41:35+08:00
tags: ["git"]
author: "七哥"
categories: ["git"]
---

## Init
```
-  git init
- git remote add origin username@host.host.com:XXXXX.git
- git add .
- git commit
- git push -u origin master
```

## branch
```
- git branch [branchName]
  新建分支
- git checkout [branchName]
  切换到分支
- git checkout -b [branchName]
  新建并切换到分支
- git checkout -b [local-branchName] [remotes/origin/branchName]
  从已存在的远端分支切出分支到本地 并跟踪远端分支
- git branch  -d  [branchName]
   删除branchName分支
- git branch  -D [branchName]
   branchName分支没有完全提交，也可以强制删除
- git branch  -m/--move [oldBranchName] [newBranchName]
   移动（重命名）分支，连同它的操作日志一起
```

## Tag 标签
```
- git tag 
  列出已有标签
- git tag  [标签名]
  新增一个`轻量级`标签
- git tag -a [标签名] -m [备注] 
  新增一个`重量级`标签
- git show [标签名]
  查看标签的详细信息
```

## log
```
1. git log [branchName]  
   显示当前分支的日志
2. git log [branchName]  filename
   显示指定分支指定的文件的日志
3. git log -g
   查看所有日志历史，包括使用git reset --hard回滚后的，丢失的日志记录
4. git reflog
   查看所有操作日志
```

## cherry-pick
```
1. git cherry-pick [commit]
   合并指定的commit到当前所在分支
```

## reset
```
1. git  reset --hard [commit]  
   重置分支到commit的状态(备注：危险！！！commit之后的修改全部丢失)
2. git reset --soft [commit]  
   重置分支到commit的状态,自从commit以来的所有改变都会显示在git  status的"Changes to be committed"中。 
```

## revert
```
1. git  revert  [commit]  
   场景：在commit 为 A的状态上进行修改，提交后的commit 为B；此时执行git revert B，代码会恢复到B的父级，也就是对commit为B的提交进行复原，执行后分支的commit 状态为A
```

## stash
```
1. git stash 
   贮藏当前不想提交的改动
2. git stash list
   列出所有贮藏的项目
3. git stash apply [stash_name]
   应用已经贮藏的项目
4. git stash drop [stash_name]
   删除已经贮藏的项目
```

## gitignore 修改后不生效
```
- 问题描述：在.gitignore中添加了某[路径]文件的忽略后，并没有生效。
- 解决办法：git rm --cached [-r] [path or name]
- 原理描述：已存在git index中的文件，无法进行忽略，需要手动删除cache
```
