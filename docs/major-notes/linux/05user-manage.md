---
title: 用户与用户组管理
date: 2021-11-25
tags:
 - Linux
categories:
 - Linux
---

### 用户管理

**useradd：** 添加用户	**-d**  指定用户登入时的目录	**-g**  初始群组	**-m** 自动创建用户的目录

```shell
#创建xiaobai用户，指定用户所属root组，家目录为/home/xiaobai
useradd xiaobai -d /home/xiaobai -g root -m
```



**passwd：** 修改指定用户密码，缺少指定用户名，默认为当前用户修改密码

```shell
#修改新建用户xiaobai用户的密码,密码为xiaobai
passwd xiaobai
```



**su：** 切换用户

```shell
#root用户切换到jack用户
su jack
```



 **usermod：**修改用户属性	**-g** 改变用户的组

```shell
#查看xiaobai用户所属的组
groups
#修改当前用户所属的组为bin组
usermod -g bin xiaobai
```



**userdel：**删除用户	**-f** 强制删除用户	**-r** 同时删除用户及用户家目录

```shell
userdel -rf xiaobai
```



### 用户组管理

**groupadd：** 创建用户组

```shell
#创建用户组user_team
groupadd user_team
```



**groupmod：**修改用户组属性	**-n** 新组名	**-g** 新的组标识号

```shell
#将用户组user_team重命名为usermod_team
groupmod -n usermod_team user_team
```



**groupdel：**删除用户组

```shell
#删除用户组名usermod_team
groupdel usermod_team
```