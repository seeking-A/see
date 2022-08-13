---
title: Redis的复制
tags:
 - Redis
categories:
 - 数据库
---

## WATH

就是我们所说的主从复制，主机数据更新后根据配置和策略，自动同步到备机的 master/slaver 机制，master 以写为主，slave 以读为主。

### 官网介绍

![image-20211127102513436](http://image.xiaobailx.top/images/20211127102513.png)



## CAN

- 读写分离
- 容灾恢复



## HOW

1. 配从不配主

2. 从库配置

   

3. 修改配置文件细节操作

   - 拷贝多个redis.conf 文件

     ![image-20211127161554186](http://image.xiaobailx.top/images/20211127161554.png)

   - 开启 daemonize yes
   - 配置对应 Pid 文件名
   - 指定端口
   - 修改 Log 文件名
   - 修改 Dump.rdb 文件名

   ![image-20211127161825984](http://image.xiaobailx.top/images/20211127161826.png)

   

4. 常用 3 招
   - 一主二仆

     一个 master 两个 slave

     ![image-20211127162859986](http://image.xiaobailx.top/images/20211127162900.png)

     > 1.切入问题：slave1、slave2 是从头开始复制还是从切入点开始复制？比如从 k4 进入，那之前 k1、k2、k3 是否也可以复制？
     >
     > 答：从头开始复制，可以复制。
     >
     > 2.从机是否可以写？set 可否？
     >
     >  答：不可
     >
     > 3.主机 shutodown 后情况如何？从机是上位还是原地待命？
     >
     > 答：主机挂机后 从机原地待命。
     >
     > 4.主机又回来后，主机新增记录，从机是否能够顺利复制？
     >
     > 答：主机回来后还是 master，从机依然能够顺利复制。
     >
     > 5.其中一台从机 down 后情况如何？依照原有它能跟上大部队吗？
     >
     > 答：从机 down 后需重新配置才能连上主机。

     

   ​	

   - 薪火相传

     上一个 slave 可以是下一个 slave 的 master，slave 同样可以接受其他 slaves 的连接和同步请求，那么该 slave 作为了链条中下一个的 master 可以有效减轻 master 的压力。

     中途变更转向会清除之前的数据，重新建立拷贝最新的 slaveof 新主库 IP 新主库端口。

     ![image-20211127165347676](http://image.xiaobailx.top/images/20211127165348.png)

     

     

   - 反客为主

     ```SLAVEOF no  one```  使当前数据库停止与其他数据库的同步，转为主数据库。

      				![image-20211127170857987](http://image.xiaobailx.top/images/20211127170858.png)



## 复制原理

slave 启动成功连接到 master 后会发送一个 sync 命令，master 接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行完毕后，master 将传送整个数据文件到 salve，以完成一次完全同步。

- 全量复制：slave 服务在接收到数据库文件数据后，在其存盘并加载到内存中。
- 增量复制：master 继续将新的所有收集到的修改命令依次传给 slave，完成同步。但只要是重新连接 master，一次完全同步（全量复制）将被自动执行。



## 哨兵模式

### WHAT

反客为主的自动版，能够后台监控主库是否出现故障，如果主库出现故障则根据投票数自动将从库转换为主库。

一组 sentinel 能同时监控多个 master。

### HOW

1. 调整结构

   主机 6379 带从机 80、81

2. 自定义的 /opt/redis-6.2.6-bak 目录下新建 sentinel.conf 文件，名字绝不能错

   ![image-20211127172513930](http://image.xiaobailx.top/images/20211127172514.png)

3. 配置哨兵

   ![image-20211127172307549](http://image.xiaobailx.top/images/20211127172307.png)

4. 正常主从演示

5. 原有的master 规律

6. 投票新选

7. 重新主从继续开工，info replication 查查看

   ![image-20211127175531939](http://image.xiaobailx.top/images/20211127175532.png)

8. 问题：如果之前的 master 重启回来，会不会双 master 冲突

   ![image-20211127180203444](http://image.xiaobailx.top/images/20211127180204.png)



## 复制的缺点

复制延迟，由于所有的写操作都是先在 master 上操作，然后同步更新到 slave 上，所以从 master 同步到 slave 机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，slave 机器数量的增加会使这个问题更加严重。