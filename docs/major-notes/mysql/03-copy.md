---
title: MySQL主从复制
date: 2021-12-15
tags:
 - MySQL
categories:
 - 数据库
---

### 主从复制

统一服务器内部署两个MySQL服务器实例，master、slave，master 服务器进行写操作，slave 服务器进行读操作。当 master 执行 DML、DDL 操作时，事务过程会保存到 relay log 中。

slave 服务器执行： start slave 命令，从服务器后台会新建两个线程，其中一个将主服务器中的 bin log 复制到 relay log 中，另外一个线程将 relay log 中的操作过程持久化到 slave 服务器中。

> DML：数据操作语言，包括对数据的增删改查
>
> DDL：数据定义语言，包括数据库、表的建立和删除
>
> DCL: 数据控制语言，包括对数据库表的权限管理

先写到 relay log，然后在写到数据库中，问题：延迟

一主多从、双主复制、多级复制