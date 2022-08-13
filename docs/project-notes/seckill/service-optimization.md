---
title: 服务优化总结
date: 2021-12-15
tags:
 - 电商秒杀
categories:
 - Java项目
---

### 核心

减少数据库访问



### 实现步骤

#### 1. 系统初始化时将商品数据加载到 redis 中；

```java
2. @Controller
@Slf4j
@RequestMapping("/seckill")
public class SeckillController implements InitializingBean {

	@Override
    public void afterPropertiesSet() throws Exception {
        List<GoodsVo> list = goodsService.findGoodsVo();
        if (CollectionUtils.isEmpty(list)){
            return;
        }
        list.forEach(goodsVo -> {
            redisTemplate.opsForValue().set("seckillGoods:"+goodsVo.getId(),goodsVo.getStockCount());
            EmptyStockMap.put(goodsVo.getId(),false);
        });

    }
}
```



#### 2. 收到秒杀请求时，使用 redis 预减库存；

```java
//预减库存
Long stock = valueOperations.decrement("seckillGoods:"+goodsId);
if (stock < 0){
    EmptyStockMap.put(goodsId,true);
    valueOperations.increment("seckillGoods:"+goodsId);
    return RespBean.error(RespBeanEnum.EMPTY_STOCK);
}
```



#### 3. 使用 RabbitMQ 进行任务排队。

```java
//发送秒杀信息
SeckillMessage seckillMessage = new SeckillMessage(user, goodsId);
mqSender.sendSeckillMessage(JsonUtil.object2JsonStr(seckillMessage));
return RespBean.success(0);
```