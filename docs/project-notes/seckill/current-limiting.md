---
title: 限流处理
date: 2021-12-9
tags:
 - 电商秒杀
categories:
 - Java项目
---

### 计数器

优点：实现简单。

缺点：无法根据系统资源动态调整接口的请求次数，造成系统资源浪费。

```java
//限流操作，每秒访问5次
ValueOperations valueOperations = redisTemplate.opsForValue();
String url = request.getRequestURI();
captcha = "0";
Integer count = (Integer) valueOperations.get(url+":"+user.getId());
if (count==null){
    valueOperations.set(url+":"+user.getId(),1,5,TimeUnit.SECONDS);
} else if(count<5){
    valueOperations.increment(url+":"+user.getId());
} else {
    return RespBean.error(RespBeanEnum.ACCESS_LIMIT_REAHCED);
}
```



### 漏斗算法

当请求过多时容易造成系统崩溃，而当请求过少时容易造成系统资源浪费。



### 令牌算法

能够对高并发请求动态快速处理。

