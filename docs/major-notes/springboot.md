## 一、Spring Boot与缓存

### JSR-107

Java Caching定义了5个核心接口，分别是CachingProvider、CacheManager、Cache、Entry 和 Expiry。

- CachingProvider定义了创建、配置、获取、管理和控制多个CacheManager。一个应用可以在运行期访问多个CachingProvider。
- CacheManager定义了创建、配置、获取、管理和控制多个唯一命名的Cache，这些 Cache 存在于 CacheManager 的上下文中。一个CacheManager仅被一个CachingProvider所拥有。
- Cache是一个类似Map的数据结构并临时存储以Key为索引的值。一个Cache仅被一个 CacheManager 所拥有。
- Entry是一个存储在Cache中的key-value对。
- Expiry 每一个存储在Cache中的条目有一个定义的有效期。一旦超过这个时间，条目为过期的状态。一旦过期，条目将不可访问、更新和删除。缓存有效期可以通过ExpiryPolicy设置。

### Spring缓存抽象

pring从3.1开始定义了org.springframework.cache.Cache
和org.springframework.cache.CacheManager接口来统一不同的缓存技术；
并支持使用JCache（JSR-107）注解简化我们开发；
• Cache接口为缓存的组件规范定义，包含缓存的各种操作集合；
• Cache接口下Spring提供了各种xxxCache的实现；如RedisCache，EhCacheCache ,
ConcurrentMapCache等；
• 每次调用需要缓存功能的方法时，Spring会检查检查指定参数的指定的目标方法是否
已经被调用过；如果有就直接从缓存中获取方法调用后的结果，如果没有就调用方法
并缓存结果后返回给用户。下次调用直接从缓存中获取。
• 使用Spring缓存抽象时我们需要关注以下两点；
1、确定方法需要被缓存以及他们的缓存策略
2、从缓存中读取之前缓存存储的数据

### 整合Redis

1. 引入spring-boot-starter-data-redis
2. application.yml配置redis连接地址
3. 使用RestTemplate操作redis
1. redisTemplate.opsForValue();//操作字符串
2. redisTemplate.opsForHash();//操作hash
3. redisTemplate.opsForList();//操作list
4. redisTemplate.opsForSet();//操作set
5. redisTemplate.opsForZSet();//操作有序set
4. 配置缓存、CacheManagerCustomizers
5. 测试使用缓存、切换缓存、 CompositeCacheManager

## 二、Spring Boot与消息

1. 大多应用中，可通过消息服务中间件来提升系统异步通信、扩展解耦能力
2. 消息服务中两个重要概念：
消息代理（message broker）和目的地（destination）
当消息发送者发送消息以后，将由消息代理接管，消息代理保证消息传递到指定目
的地。
3. 消息队列主要有两种形式的目的地
1. 队列（queue）：点对点消息通信（point-to-point）
2. 主题（topic）：发布（publish）/订阅（subscribe）消息通信

## 三、Spring Boot与检索
## 四、Spring Boot与任务
## 五、Spring Boot与安全
## 六、Spring Boot与分布式
## 七、Spring Boot与监控管理

## 八、Spring Boot与部署