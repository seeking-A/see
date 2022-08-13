---
title: Redis的发布和订阅
tags:
 - Redis
categories:
 - 数据库
---

## WATH

进程间的一种消息通信模式，发送者（pub）消息，订阅者（sub）接收消息。

**订阅/发布消息图**

![image-20211127111951939](http://image.xiaobailx.top/images/20211127111952.png)



![image-20211127112611600](http://image.xiaobailx.top/images/20211127112611.png)



## 命令

- PSUBSCRIBE patterm [patterm] ：订阅一个或多个符合给定模式的频道。
- PUBSUB subcommand [argument [argument]] ：查看订阅与发布系统状态。
- PUBLISH channel message ：将信息发送到指定的频道。
- PUNSUBSCRIBE [patterm [patterm]] ：退订所有给定模式的频道。
- SUBSCRIBE channel [channel] ：订阅给定一个或多个频道的信息。
- UNSUBSCRIBE [channel [channel]] ：指退订给定的频道。



## 案例

**SUBSCRIBE  一次性订阅多个**

![image-20211127113831165](http://image.xiaobailx.top/images/20211127113831.png)

**PSUBSCRIBE  订阅多个**

![image-20211127114413699](http://image.xiaobailx.top/images/20211127114414.png)