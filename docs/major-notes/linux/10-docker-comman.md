---
title: Docker
date: 2021-11-26
tags:
 - Linux
categories:
 - Linux
---

### docker 常用命令

**docker search：** 搜索在线可用的镜像名

```shell
#搜索centos镜像
docker search centos
```



**docker pull：** 从官网拉取镜像

```shell
#拉取centos镜像
docker pull centos
```



**docker images：** 查看所有下载的镜像

```shell
#查看所有镜像
docker images
```



**docker run：** 创建一个指定容器   		**--name：** 命名启动的容器镜像命名

**-d** 指定容器在后台运行，返回容器id 	**-i** 以交互模式运行容器 通常与-t同时使用	**-t** 为容器重新分配一个伪终端

```shell
#启动centos容器将其命名为dev_centos，并连接到系统中
docker run --name dev_centos -it centos /bin/bash
#退出登录的docker容器，并关闭容器
exit
# 退出登录的docker容器，不关闭容器
ctrl+q+p
```



**docker start <NAME or ID> ：**  启动容器

```shell
#启动容器
docker start dev_cnetos
```



**docker exec：**  连接正在运行的容器

```shell
#连接正在运行的容器
docker exec -it dev_centos /bin/bash
```



**docker ps：**  查看正在运行的容器		**-a**  显示所有容器

```shell
#查询所有正在运行的容器
docker ps -a
```



**docker stop <NAME or ID> ：**  启动容器

```shell
docker stop dev_centos
```



**docker rmi -f <ID>：**  删除镜像前，需要先删除所有使用镜像的容器

```
docker rmi -f dev_centos
```



**docker rm <NAME or ID>：**  删除容器

```shell
docker rm dev_centos
```

