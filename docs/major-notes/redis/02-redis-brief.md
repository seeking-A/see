---
title: Redis入门概述
tags:
 - Redis
categories:
 - 数据库
---


###  WHAT

> Redis (Remote Dictionary Service) , 指的是远程字典服务器，是完全开源免费，由 C 语言编写的，遵守 BSD 协议的一个高性能（key/value）分布式内存数据库，基于内存运行并支持持久化的 NoSQL 数据库，是当下热门的 NoSQL 数据库之一，也被人们称为数据库结构服务器。
>
> Redis 与其他 key - value 缓存产品有以下三个特点：
>
> - 支持数据持久化，可以将内存中的数据保存到磁盘中，重启时可以重新加载进行使用。
> - 不仅仅支持 Key - value 类型数据，还提供 list、set、zset、hash 等数据结构的存储。
> - 支持数据的备份，即 master - slave 模式的数据备份。



###  CAN

>- 内存存储和持久化，支持异步将内存中的数据写到硬盘上，同时不影响继续服务。
>
>- 取最新 N 个数据操作，如：可以将最新的 10 条评论的 ID 放在 Redis 的 List 集合中。
>- 模拟类似于 HttpSession 这种需求设定过期时间的功能。
>- 发布、订阅消息系统。
>- 定时器，计数器



###  WHERE

https://redis.io/ 、http://www.redis.cn/

###  HOW

> - 数据类型、基本操作和配置
> - 持久化和复制、RDB / AOF
> - 事务的控制
> - 复制 ......



### VMWARE + VMTools 的安装

[VMWARE + VMTools 的安装]()



## Redis 安装

由于企业里面使用 Redis 基本不涉及 Windows 版，因此企业实战请认准 Linux 版。安装步骤 如下：

1. 下载 redis -3.0.4.tar.gz 安装包将其放到 Linux 系统的 /opt 目录下
2. /opt 目录下解压安装包，命令：tar -zxvf redis-3.0.4.tar.gz，解压完成后出现 redis-3.0.4 目录
3. 进入 redis-3.0.4 目录，命令：cd redis-3.0.4
4. 在 redis-3.0.4 目录下执行 make 命令，运行 make 命令时出现错误解决步骤：
   - 安装 GCC ，能上网，使用命令：yum install gcc c++ ,不能上网，使用 [磁盘挂载安装]()
   - 二次 make，若提示 jemalloc/jemalloc.h 错误，先运行命令：make distclean ，然后再次运行：make
   - Redis test 不需要执行
5. 如果 make 完成后继续 执行 make install
6. 查看默认安装目录：usr/local/bin
   - Redis-benchamark：性能测试工具，可以用于性能测试
   - Redis-check-aof：修复有问题的 AOF 文件
   - Redis-check-dump：修复有问题的 dump.rdb 文件
   - Redis-cli：客户端，操作入口
   - Redis-sentinel：Redis 集群使用
   - Redis-server：Redis 服务器启动命令
7. 启动
   - 修改 redis.conf 文件将里面的daemonize no 改为 yes，使服务在后台启动
   - 将默认的 redis.conf 拷贝到自己定义的一个路径下，比如 /redis-6.2.6-bak
   - 启动命令：redis-server /opt/redis-6.2.6/redis.conf
   - 连通测试：redis-cli ping
8. HelloWorld
9. 关闭
   - 单实例关闭：redis-cli shutdown
   - 多实例关闭：指定端口关闭：redis-cli -p 6379 shutdown



Redis 启动后杂项基础知识

**单进程：**单进程模型来处理客户端的请求，对读写事件的响应是通过对 epoll 函数的包装来做到的。Redis 的实际处理速度完全依靠主进程的执行效率。

Epoll 是 Linux 内核为处理大批量文件描述符而做了改进的 epoll，是 Linux 下多路复用 IO 接口 select/poll 的增强版本，能显著增强提高程序在大量并发连接中只有少量活跃的情况下的系统 CPU 利用率。

**常用命令：**

```shell
- select：切换数据库命令
- dbsize：查看当前数据库的Key数量
- flushdb：清空当前库
- flushall：清空所有库
```

Redis 默认有 16 个数据库，类似数组下标从 0 开始，初识默认使用零号库，可以使用 SELECT <dbid> 命令来连接指定的数据库 ID；16个数据库采用统一密码管理，每个库的密码相同；Redis 索引都是从 0 开始，其默认端口为 6379 。





 

