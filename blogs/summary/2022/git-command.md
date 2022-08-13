---
title: Git常用命令整理
date: 2022-07-08
tags:
 - Git
categories:
 - 工具
---

### 常用命令

```shell
//初始化工作区  
git init
//查看工作区状态
git status
//将文件添加到暂存区
git add <file>
//将文件从暂存区中移除
git rm --cached <file>

//将文件提交到本地库
git commit -m "version" <file>
//查看精简日志信息
git reflog  
//查看详细日志信息
git log  

//本地回滚
git reset  <versionID>
//本地远程回滚
git revert  <versionID>
```



### 分支操作

```shell
//查看分支
git branch -v
//创建分支
git branch <branchName>
//切换分支 
git checkout <branchName>
//创建分支同时切换分支
git checkout -b <branchName>
//合并分支，需在被合并的分支上进行操作
git merge <branchName>
//合并分支，需在被合并的分支上进行操作
get rebase <barchName>
```



### 分离HEAD

分离的 HEAD 就是让其指向了某个具体的提交记录而不是分支名

> HEAD 是一个对当前检出记录的符号引用 —— 也就是指向你正在其基础上进行工作的提交记录。
>
> HEAD 总是指向当前分支上最近一次提交记录。大多数修改提交树的 Git 命令都是从改变 HEAD 的指向开始的。
>
> HEAD 通常情况下是指向分支名的（如 bugFix）。在你提交时，改变了 bugFix 的状态，这一变化通过 HEAD 变得可见。

```shell
//查看HEAD指向
cat .git/HEAD
//分离HEAD
git checkout <referenceID>
```



### 相对引用

- 使用 `^` 向上移动 1 个提交记录
- 使用 `~<num>` 向上移动多个提交记录，如 `~3`

```shell
//将HEAD指向所在分支的父提交
git checkout <branchName>^
//将HEAD回退到所在分支num步
git checkout <branchName>~<num>
//强制将分支指向一个提交
git branch -f <branchName> <referenceID>~<num>
```



### 撤销变更

```shell
//撤回本地仓库提交记录
git reset <referenceId>
//撤回提交记录，同时对远程仓库生效
git revert <referenceId>
```



### 整理提交记录

```shell
//将提交复制到当前所在的位置
git cherry-pick <referenceID>
//使用交互式方式调整
git rabase -i <referenceId>
//给提交设置标签名
git tag tagName <referenceId>
//当前所在位置的锚点描述
git describe
```



### 远程仓库

```shell
//将远程仓库克隆至本地库
git clone <url>
//将本地仓库中的远程分支更新成远程仓库相应分支最新的状态
git fetch
//将远程仓库代码拉取到本地库，fetch + merge
git pull <nickname> <branchName>
//解决代码偏移，fetch + rebase
git pull --rebase
//将本地库代码上传至远程仓库
git push <nickname> <branchName>
//代码追踪
//通过远程仓库检出新的分支
git checkout -b <branchName> <remote>
//设置远程追踪分支
git branch -u <remote> <branchName>
```



### 跨团队协作

```shell
//查看当前所有远程地址别名
git remote -v 
//创建远程仓库别名
git remote add <nickname> <url>

//解决代码冲突
//1.编辑含代码冲突文件，手动消除冲突然后保存
//2.将修改后的文件添加至暂存区
git add <file>
//3.将文件提交至本地库
git commit -m <branchName>

//解决Remote Rejected
//1.创建新的分支并上传至远程分支
//2.重置远程仓库
git reset <remote>
//3.将remote分支更新到最新状态
```



[ Git详解及版本控制规范](https://learngitbranching.js.org/)

[一个让学 Git 命令变得好玩又有趣的神奇网站](https://blog.csdn.net/best_luxi/article/details/123747486)

