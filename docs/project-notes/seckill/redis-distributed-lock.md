---
title: Redis分布式锁
date: 2021-12-12
tags:
 - 电商秒杀
categories:
 - Java项目
---

## Redis 分布式锁

### 分布式锁

进来一个线程先占位，当别的线程进来操作时，发现已经有人占位了，就会放弃或者稍后再试。线程操作执行完成后，需要调用 ```del``` 指令释放位置。

```java
@SpringBootTest
class SeckillDemoApplicationTests {

    @Autowired
    private RedisTemplate redisTemplate;

    /**
     * 分布式锁
     * 进来一个线程先占位，当别的线程进来操作时，发现已经有人占位了，就会放弃或者稍后再试
     * 线程操作执行完成后，需要调用del指令释放位子
     */
    @Test
    public void testLock01() {
        ValueOperations valueOperations = redisTemplate.opsForValue();
        Boolean isLock = valueOperations.setIfAbsent("k1", "v1");
        if (isLock) {
            valueOperations.set("name", "xxxx");
            String name = (String) valueOperations.get("name");
            System.out.println(name);
            redisTemplate.delete("k1");
        } else {
            System.out.println("有线程在使用，请稍后");
        }
    }
}
```



为了防止业务执行过程中抛异常或者挂机导致 ```del``` 指定没法调用形成死锁，可以添加超时时间。

```java
@Test
public void testLock02() {
    ValueOperations valueOperations = redisTemplate.opsForValue();
    Boolean isLock = valueOperations.setIfAbsent("k1", "v1", 5, TimeUnit.SECONDS);
    if (isLock) {
        valueOperations.set("name", "xxxx");
        String name = (String) valueOperations.get("name");
        System.out.println(name);
        redisTemplate.delete("k1");
    } else {
        System.out.println("有线程在使用，请稍后");
    }
}
```



上面例子，如果业务非常耗时会紊乱。

**举例：** 第一个线程首先获得锁，然后执行业务代码，但是业务代码耗时8秒，这样会在第一个线程的任务还未执行成功锁就会被释放，这时第二个线程会获取到锁开始执行，在第二个线程开执行了3秒，第一个线程也执行完了，此时第一个线程会释放锁，但是注意，他释放的第二个现成的锁，释放之后，第三个线程进来。

**解决方案：** 尽量避免在获取锁之后，执行耗时操作；将锁的 value 设置为一个随机字符串，每次释放锁的时候，都去比较随机字符串是否一致，如果一致，再去释放，否则不释放。



### Lua脚本

**Lua脚本优势：** 

1. 使用方便，Redis 内置了对 Lua 脚本的支持；
2. Lua 脚本可以在 Rdis 服务端原子的执行多个Redis 命令；
3. 由于网络在很大程度上会影响到 Redis 性能，使用 Lua 脚本可以让多个命令一次执行，可以有效解决网络给 Redis 带来的性能问题。

**使用Lua脚本思路：** 

1. 提前在 Redis 服务端写好 Lua 脚本，然后在 Java 客户端去调用脚本；
2. 可以在java客户端写 Lua 脚本，写好之后，去执行。需要执行时，每次将脚本发送到 Redis上去执行

> 1. 创建Lua脚本 lock.lua (放在resources目录下)

```lua
if redis.call("get",KEYS[1]) == ARGV[1] then
    return redis.call("del",KEYS[1])
else
    return 0
end
```

> 2. 调用脚本 RedisConfig.java

```java
@Bean
public DefaultRedisScript<Boolean> script() {
 DefaultRedisScript<Boolean> redisScript = new DefaultRedisScript<>();
 //放在和application.yml 同层目录下
 redisScript.setLocation(new ClassPathResource("lock.lua"));
 redisScript.setResultType(Boolean.class);
 return redisScript;
}
```

> 3. SeckillDemoApplicationTests.java

```java
@Autowired
private RedisScript<Boolean> script;

@Test
public void testLock03(){
    ValueOperations valueOperations = redisTemplate.opsForValue();
    String value = UUID.randomUUID().toString();
    //给锁添加一个过期时间，防止应用在运行过程中抛出异常导致锁无法及时得到释放
    Boolean isLock = valueOperations.setIfAbsent("k1",value,5, TimeUnit.SECONDS);
    //没人占位
    if (isLock) {
        valueOperations.set("name","xxxx");
        String name = (String) valueOperations.get("name");
        System.out.println(name);
        System.out.println(valueOperations.get("k1"));
        //释放锁
        Boolean result = (Boolean)redisTemplate.execute(script, Collections.singletonList("k1"),value);
        System.out.println(result);
    }else {
        //有人占位，停止/暂缓 操作
        System.out.println("有线程在使用，请稍后");
    }
}
```