---
title: Redis事务
tags:
 - Redis
categories:
 - 数据库
---

## WHAT

Redis 事务允许一次性执行多个命令，其本质上是 一组命令的集合。一个事务中的所有命令都会序列化，**按顺序地串行执行而不会被其他命令插入，不允许加塞。**

### 官网介绍

![image-20211127102859979](http://image.xiaobailx.top/images/20211127102900.png)



## CAN

一个队列中，一次性、顺序性、排他性地执行一系列命令



## HOW

### 常用命令

- DISCARED ：取消事务，放弃执行事务块内所有命令
- EXEC ： 执行所有事务块内的命令
- MULTI ： 标记一个事务块的开始
- UNWATCH ： 取消 WATCH 命令对所有 KEY 的监视
- WATCH key [key] ： 监视一个（或多个）key，如果在事务执行之前这个（或这些）key 被其他命令所改动，那么事务将被打断。



### 执行策略

- **正常执行**

```shell
#清空数据库
flushdb
#标记事务开始
MULTI
#设置id值为12
set id 12
#获取id的值
get id
#对k1的值进行加1操作
incr k1
#对k1的值进行加1操作
incr k1
#获取k1的值
get k1
#执行事务内所有命令
EXEC
```

![image-20211126110541082](C:/Users/20689/AppData/Roaming/Typora/typora-user-images/image-20211126110541082.png)



- **放弃事务**

```shell
MULTI
#设置name值为xiaobai
set name xiaobai
#设置age值为18
set age 18
#对k1的值进行加1操作
incr k1
#放弃执行事务块的所有命名
discard
#查看所有键值
keys *
```

![image-20211126111444560](http://image.xiaobailx.top/images/20211126111445.png)



- **全体连坐**

```shell
#查看所有键值
MULTL
#设置name值为xiaobai
set name xiaobai
#查看name的值
get name 
#对k1的值进行加1操作
incr k1
#查看k1的值
get k1
#设置email值，故意不传参
set email 
#执行事务块中所有命令
exec
#查看所有键值
keys *
```

![image-20211126112208784](http://image.xiaobailx.top/images/20211126112209.png)



- **冤头债主**

```shell
#查看所有键值
MULTL
#设置name值为xiaobai
set name xiaobai
#设置age值为18
set age 18
#设置email值为xiaobai@qq.com
set email xiaobai@qq.com
#故意对email的值进行加1操作
incr k1
#查看age的值
get age
#执行事务块中所有命令
exec
```

![image-20211126114033253](http://image.xiaobailx.top/images/20211126114033.png)



- **WATCH 监控**

> **悲观锁、乐观锁、CAS**
>
> 悲观锁：每次拿数据时都认为别人会修改，所以每次拿数据时都上锁。传统关系型数据库中的行锁、表锁，读锁、写锁都是在做操作之前先上锁
>
> 乐观锁：每次拿数据时都认为别人不会修改，所以不会上锁，但在更新时会判一下在此期间别人有没有更新这个数据，可以使用版本号等机制。乐观锁适用于多读应用，以提高吞吐量。



**案例**

```shell
##初始化信用卡可用余额和欠额
set balance 100
set debt 0
multi
decrby balance 30
incrby debt 30
exec
```

![image-20211126115340578](http://image.xiaobailx.top/images/20211126115340.png)



```shell
##无加塞篡改，先监控在开启multi，保证两笔金额变动在同一事务内
watch balance
multi
decrby balance 10
incrby debt 10
exec
```

![image-20211126115955296](http://image.xiaobailx.top/images/20211126115955.png)



```shell
##有加塞篡改，监控key，如果key被修改了，后面的一个事务的执行失效
WATCH balance
set balance 
multi
decrby balance 15
incrby debt 15
exexc
```

![image-20211126120613053](http://image.xiaobailx.top/images/20211126120613.png)



```shell
#unwatch,一旦执行了exec之前加的监控锁都会被取消
watch balance
set balance 300
set balance 350
unwatch
multi
set balance 100
set debt 0
exec
get balance
```

![image-20211126123115828](http://image.xiaobailx.top/images/20211126123116.png)



### 3 阶段

开启：以 MULTI 开始一个事务

入队：将多个命令入队到事务中，接到这些命令并不会立即执行，而是放到等待执行的队列里边

执行：有 EXEC 命令触发事务



### 3 特性

- 单独的隔离操作：事务中的所有命令都会序列化，按顺序地执行。事务在执行过程中，不会被其他客户端发送来的命令请求所打断。
- 没有隔离级别的概念：队列中的命令没有提交之前都不会实际的被执行，因为事务提交之前任务指令多不会被实际执行，也就不存在”事务内的查询要看到事务里的更新，在事务外查询不能看到“这个让人万分头疼的问题。
- 不保证原子性：Redis 同一个事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚。

