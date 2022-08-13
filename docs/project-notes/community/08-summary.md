---
title: 项目总结
date: 2022-01-17
tags:
 - 仿牛客社区
categories:
 - Java项目
---

## MySQL

### 存储引擎

5.1 以后默认存储引擎为 InnoDB ，支持事务和外键，绝大部分场景使用 InnoDB。

### 事务

- 事务的特性

  原子性、一致性、隔离性、持久性

- 事务的隔离性

  并发异常：第一类丢失更新、第二类丟失更新、脏读、不可重复读、幻读

  隔离级别：Read Uncommitted、 Read Committed、 Repeatable Read、 Serializable

- Spring 事务管理

  声明式事务

  编程式事务

### 锁

![image-20220216165742630](http://image.xiaobailx.top/images/20220216165742.png)

![image-20220216170436182](http://image.xiaobailx.top/images/20220216170436.png)

![image-20220216170558895](http://image.xiaobailx.top/images/20220216170559.png)

![image-20220216170806934](http://image.xiaobailx.top/images/20220216170807.png)



### 索引

![image-20220216171458741](http://image.xiaobailx.top/images/20220216171458.png)

![image-20220216171640992](http://image.xiaobailx.top/images/20220216171641.png)



## Redis

### 数据类型

![image-20220216171925586](http://image.xiaobailx.top/images/20220216171925.png)



### 过期策略

![image-20220216172018039](http://image.xiaobailx.top/images/20220216172018.png)



### 淘汰策略

![image-20220216172901078](http://image.xiaobailx.top/images/20220216172901.png)

![image-20220216173002014](http://image.xiaobailx.top/images/20220216173002.png)



### 缓存穿透

![image-20220216175433127](http://image.xiaobailx.top/images/20220216175433.png)



### 缓存击穿

![image-20220216175520889](http://image.xiaobailx.top/images/20220216175521.png)



### 缓存雪崩

![image-20220216175617540](http://image.xiaobailx.top/images/20220216175617.png)



### 分布式锁

![image-20220216175732640](http://image.xiaobailx.top/images/20220216175732.png)

![image-20220216175807512](http://image.xiaobailx.top/images/20220216175807.png)

![image-20220216180001740](http://image.xiaobailx.top/images/20220216180001.png)

![image-20220216180046352](http://image.xiaobailx.top/images/20220216180046.png)



## Spring

### Spring IOC

![image-20220216180231671](http://image.xiaobailx.top/images/20220216180231.png)



### Spring AOP

![image-20220216180424789](http://image.xiaobailx.top/images/20220216180424.png)



### SpringMVC

![image-20220216175246185](http://image.xiaobailx.top/images/20220216175246.png)