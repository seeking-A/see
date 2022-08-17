---
title: Redis的数据类型
tags:
 - Redis
categories:
 - 数据库
---

## Redis 的五大数据类型简介

**String (字符串)：** String 是redis 最基本的数据类型，一个 key 对应一个 value，一个 value 最多可以是 512M。

**Hash (哈希)：** Hash 是一个键值对集合，类似于 Java 中的 Map<String,Object>，适合用于存储对象

**List (列表)：** List 是简单的字符串列表，可以根据插入顺序排序。我们可以添加一个元素到列表的头部（左边）或尾部（右边），其底层实际上是一个链表。

**Set (集合)：** Set 是 String 类型的无序集合，是通过 HashTable 实现的。

**Zet (有序集合)：** Zset 和 Set 一样也是 String 类型的集合，其不允许有重复的成员。不同的是，每个元素都会关联一个 Double 类型的分数，Redis 正是通过分数来为集合中的成员进行从小到大的排序。Zset 的成员是唯一的，但分数 (Score) 是可以重复的。



## Redis 常见数据类型操作命令

详见 [Redis 命令参考手册](http://redisdoc.com/)



### Redis 键（Key）操作命令

- DEL key ：用于在 key 存在时删除 key
- DUMP key ：序列化给定的 key，并返回被序列化的值
- EXISTS key ：检查给定的 key 是否存在
- EXPIRE key ：为给定 key 设置过期时间
- EXPIREAT key timestamp ：与 EXPIRE 类似，都用于为 key 设置过期时间，不同在于 EXPIPEAT 命令接收的参数为时间戳
- PEXPIRE key milliseconds-timestamp ：设置 key 过期的时间戳，以毫秒计
- KEYS pattern ：查找所有符合给定模式的 key
- MOVE key db ：将当前数据库的 key 移动到给定的数据库中
- PERSIST key ：移除 key 的过期时间，key 将持久保持
- PTTL key ：以毫秒单位返回 key 的剩余时间
- TTL key ：以秒为单位，返回给定 key 的剩余生存时间，-1表示永不过期，-2 表示已过期
- RANDOMEY ：从当前数据库中随机返回一个 key
- RENAME key ：修改 key 的名称
- RENAMENX key newkey ：仅当 newkey 不存在时，将 key 改为 newkey
- TYPE key ： 返回所存储的值类型



**keys / del / exists** 演示

```shell
#连接redis客户端
redis-cli 
#测试连通性
ping
#设置 k1
set k1 v1
#设置 k2
set k2 v2
#设置 k3
set k3 v3
#查看当前数据库中所有key
keys *
#删除 k1
del k1
#查看当前数据库中所有key
keys *
```

![image-20211124164159314](http://image.xiaobailx.top/images/20211124164159.png)



**/ move /expire / ttl / type**  演示

```shell
#将当前数据库中的k2移动到2号数据库中
move k2 2
#查看当前数据库中所有key
keys *
#切换到2号数据库
select 2
#查看当前数据库中所有key
keys *
#为k4设置过期时间
expire k4 4
#查看k4剩余生产时间
ttl k4
#查看k2剩余生存时间
ttl k2
#查看k2数据类型
type k2
#退出
exit
```

![image-20211124165337432](http://image.xiaobailx.top/images/20211124165337.png)



### Redis 字符串（String）操作命令

- SET key value ：设置指定 key 的值
- GET key ：获取指定 key的值
- GETRANGE key start end ：获取给定的 key 中字符串的子字符串
- GETSET key value ： 将给定的 key 的值设为 value，并返回 key 的旧值
- GETBIT key offset ：对 key 所存储的字符串值，获取指定偏移量上的 bit
- MGET key1 [key2] ：获取所有 (一个或多个) 给定 key 的值
- SETBIT key offset value ：对 key 所存储的字符串值，设置或清除指定偏移量上的位
- SETEX key seconds value ：将值 value关联到 key，并将 key 的过期时间设为 seconds (以秒为单位)
- SETNX key value ：只有在 key 不存在时设置 key 的值
- SETRANGE key offset value ：用 value 参数覆写给定 key 所存储的字符串值，从偏移量 offset 开始
- STRLEN key ：返回 key 所存储的字符串值的长度
- MSET key value [key value] ：同时设置一个或多个 key - value
- PSETEX key millseconds value ：与 SETEX 命令相似，都是为给定 key 设置生存时间，不同的是 PSETEX 是以毫秒设置 key 的生存时间
- INCR key ：将 key 中存储的数字值增 1
- INCRBY key increment ：将 key 所存储的值加上给定的增量值 (increment)
- INCRBYFLOAT key increment ：将 key 所存储的值加上给定的浮点增量值 (increment)
- DECR key ：将 key 中存储的数字值减 1
- DECRBY key decrement ：key 所存储的值减去给定的减量值
- APPEND key value ：如果 key 已存在并且是一个字符串，APPEND 命令将 value 追加到 key 原来值得末尾



**set / get / del / append / strlen**  演示

```shell
#连接Redis客户端
redis-cli
#查看当前数据库所有key
keys *
#设置 k1
set k1 v1
#获取 k3
get k3
#删除 k3
del k3
#查看当前数据库所有key
keys *
#将"_k1"追加到k1末尾
append k1 _k1
#查看k1值的长度
strlen k1
#查看当前数据库所有key
keys *
#查看k1的值
get k1
```

![image-20211124173010573](http://image.xiaobailx.top/images/20211124173011.png)



**incr / decr / incrby / decrby** 演示，一定要是数字才能进行加减

```shell
#连接Redis客户端
redis-cli
#选择1号数据库
select 1
#查看当前数据库中所有值
keys *
#设置 k1
set k1 1
#使k1增1
incr k1
#查看k1的值
get k1
#使k1减1
decr k1
#查看k1的值
get k1
#使k1加10
incrby k1 10
#查看k1的值
get k1
#使k1减10
decrby k1 10
#查看k1的值
get k1
```

![image-20211124174000152](http://image.xiaobailx.top/images/20211124174000.png)



**getrange / setrange** 演示

```shell
#连接Redis客户端
redis-cli
#查看当前数据库所有值
keys *
#查看k1的值
get k1
#截取k1下标0~2的子字符串
getrange k1 0 2
#用"hello_redis"覆盖k1下标2之后的字符
setrange k1 2 hello_redis
#查看k1的值
get k1
```

![image-20211124175055221](http://image.xiaobailx.top/images/20211124175055.png)



**setex / setnx** 演示

```shell
#连接Redis客户端
redis-cli
#查看当前数据库所有值
keys *
#设置 k1 生存时间为10s
setex k1 10 hello
#查看k1剩余生存时间
ttl k1
#查看k1剩余生存时间
ttl k1
#查看k1剩余生存时间
ttl k1
#获取k1的值
get k1
#如果k1存在设置K1的值为hello
setnx k1 hello
#如果k1存在设置K1的值为hello_redis
setnx k1 hello_redis
#获取k1的值
get k1
```

![image-20211124195028800](http://image.xiaobailx.top/images/20211124195029.png)



**mset / mget / msetnx** 演示

```shell
#连接Redis客户端
redis-cli
#切换到2号数据库
select 2
#查看当前数据库中所有值
keys *
#批量设置k1、k2、k3
mset k1 v1 k2 v2 k3 v3
#查看k1的值
get k1
#查看k2的值
get k2
#查看k3的值
get k3
#批量获取k1、k2、k3的值
mget k1 k2 k3
```

![image-20211124195817559](http://image.xiaobailx.top/images/20211124195817.png)



**getset** 演示

```shell
#连接Redis客户端
redis-cli
#查看当前数据库中所有值
keys *
#获取k1的值
get k1
#获取k1的值并将其值设置为hello_redis
getset k1 hello_redis
#获取k1的值
get k1
```

![image-20211124200336971](http://image.xiaobailx.top/images/20211124200337.png)



### Redis 列表（List）操作命令

- BLPOP key1 [key2] timeout ：移除并获取第一个元素，如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止
- BRPOP key1 [key2] timeout ：移除并获取列表最后一个元素，如果列表没有元素会阻塞列表直到等待超时或可发现可弹出元素为止
- BRPOPLPUSH source destination timeout ：从列表中弹出一个值，将弹出的元素插入到另一个列表中并返回它，如果列表没有元素会阻塞列表直到等待超时或可发现可弹出元素为止
- LINDEX key index ：通过索引获取列表中的元素
- LINSERT key BEFOREIAFTER pivot value ：在列表的元素前或者后插入元素
- LLEN key ：获取列表长度
- LPOP key ：获取列表长度
- LPUSH key value ：移除并获取列表中第一个元素
- LPUSHX key value ：将一个或多个值插入到已存在的列表头部
- LPUSHX key start stop ：获取列表指定范围内的元素
- LREM key count value ：移除列表元素
- LSET key index value ：通过索引来设置元素列表
- LTRIM key start stop ：对一个列表进行修剪，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除
- RPOP key ：移除并获取列表最后一个元素
- RPOPLPISH source destination ：移除列表中最后一个元素，并将该元素添加到另一个列表并返回
- RPISH key value1 [value2] ：在列表找那个添加一个或多个值
- RPUSHX key value ：为已存在的列表添加值



**lpush / rpush / lrange** 演示

```shell
#清空数据库
flushdb
#在l1头部插入e1、e2、e3
lpush l1 e1 e2 e3
#查看l1的所有元素
lrange l1 0 3
#在l1尾部插入e4、e5、e6
rpush l1 e4 e5 e6
#查看l1的所有元素
lrange l1 0 6
```

![image-20211125103920426](http://image.xiaobailx.top/images/20211125103920.png)



**lpop / rpop** 演示

```shell
#查看l1的所有元素
lrange l1 0 6
#从l1头部弹出一个元素
lpop l1 1
#从l1尾部弹出两个元素
rpop l1 2
```

![image-20211125104425605](http://image.xiaobailx.top/images/20211125104425.png)



**lindex / llen / lrem**  演示

```shell
#查看l1的所有元素
lrange l1 0 -1
#获取l1的元素个数
llen l1
#从l1头部移除2个e3
lrem l1 2 e3
#查看l1的所有元素
lrange l1 0 -1
```

![image-20211125105834391](http://image.xiaobailx.top/images/20211125105834.png)



**rpoplpop / ltrim / lset** 演示

```shell
#查看l1中所有元素
lrange l1 0 -1
#移除l1中最后一个元素并将其添加到l2中
rpoplpush l1 l2
#查看l1中所有元素
lrange l1 0 -1
#查看l2中所有元素
lrange l2 0 -1
#截取l1索引范围为0到2的元素然后赋值给l1
ltrim l1 0 2
#查看l1中所有元素
lrange l1 0 -1
#从l1头部开始查找索引值为2的元素并设置其值为e0
lset l1 2 e0
#查看l1中所有元素
lrange l1 0 -1
```

![image-20211125110508053](http://image.xiaobailx.top/images/20211125110508.png)

![image-20211125111340482](http://image.xiaobailx.top/images/20211125111340.png)



**linsert key before / after value1 value2** 演示

```shell
#查看l1中所有元素
lrange l1 0 -1
#在l1中e2前插入元素e3
linsert l1 before e2 e3
#在l1中e0后插入元素e-1
linsert l1 after e0 e-1
#查看l1中所有元素
lrange l1 0 -1
```

![image-20211125112709070](http://image.xiaobailx.top/images/20211125112729.png)

>**总结：**
>
>- list 是一个字符串链表，left、right 都可以添加。如果键不存在，则创建新链表；如果键以存在，则新增内容；如果值全部被移除，则对应的键也会消失。
>- 链表的操作无论是头还是尾操作，效率都极高，但是如果是对中间元素操作，效率就很惨淡了。



### 集合（Set）常用操作

- SADD key member1 [member2] ：向集合中添加一个或多个成员

- SCARD key ：获取集合中的成员
- SDIFF key [key2] ：返回给定所有集合的差集
- SDIFFSTORE destination key1 [key2] ：返回给定所有集合的差集并存储在 destination 中
- SINTER key1 [key2] ：返回所有集合的交集
- SINTERSTORE destination key1 [key2] ：返回给定所有集合的交集并存储在 destination 中
- SISMEMNER key number ：判断 member 元素是否是集合 key 的成员
- SMEMBERS key ：返回集合中所有成员
- SMOVE source destinastion ：将 member 元素是否是集合 key 的成员
- SPOP key ：移除并返回集合中一个或多个随机数
- SRANDMEMBER key [count] ：返回一个集合中一个或多个随机数
- SREM key member1 [member2] ：移除集合中一个或多个成员
- SUNION key1 [key2] ：返回所有给定集合的并集
- SUNIONSTORE destination key1 [key2] ：所有给定集合的并集存储在 destination 集合中
- SSCAN key cursor [MATCH patterm] [COUNT count] ：迭代集合中的元素



**sadd / smembers / sismember** 演示

```shell
#连接Redis客户端
redis-cli
#查看当前数据库存在数据数量
dbsize
#向集合s1中添加元素v1
sadd s1 v1
#向集合s1中添加相同元素v1
sadd s1 v1
#向集合s1中添加元素v2、v3、v4、v5
sadd s1 v2 v3 v4 v5
#查看集合s1中的元素
smembers s1
```

![image-20211124205741555](http://image.xiaobailx.top/images/20211124205741.png)



**scard / srem / srandmember** 演示

```shell
#获取集合里面的元素个数
scard s1
#删除集合s1中的元素v1
srem s1 v1
#从s1中随机取出2个元素
srandmember s1 2
#从s1中随机取出2个元素
srandmember s1 2
```

![image-20211124211106669](http://image.xiaobailx.top/images/20211124211106.png)



**spop / stop / smove** 演示

```shell
#查看集合s1中的元素
smembers s1
#随机出栈1个元素
spop s1 1
#随机出栈1个元素
spop s1 1
#将s1中的v1赋给s1
smove s1 s2 v1
```

![image-20211124212414082](http://image.xiaobailx.top/images/20211124212414.png)

```shell
#清空数据库
flushdb
#查看当前数据库所有数据
keys *
#向集合s1添加元素1、2、3、4、5
sadd s1 1 2 3 4 5
#向集合s2添加元素1、2、a、b、c
sadd s2 1 2 a b c
#获取集合s1与s2的差集
sdiff s1 s2
#获取集合s2与s1的差集
sdiff s2 s1
#获取集合s1与s2的交集
sinter s1 s2
#获取集合s1与s2的并集
sunion s1 s2
```

![image-20211124213234651](http://image.xiaobailx.top/images/20211124213235.png)

![image-20211124213300211](http://image.xiaobailx.top/images/20211124213300.png)



### Redis 哈希（Hash）操作命令

- HDEL key field1 [field2] ： 删除一个或多个哈希表字段
- HEXISTS key field ：查看哈希表 key 中，指定的字段是否存在
- HGET key field ：获取存储在哈希表中指定字段的值
- HGETALL key ：获取在哈希表中指定 key 的所有字段和值
- HINCRBY key field increment ： 为哈希表 key 中的指定字段的整数值加上增量 increment 。

- HINCRBYFLOAT key field increment ： 为哈希表 key 中的指定字段的浮点数值加上增量 increment
- HKEYS key ： 获取所有哈希表中的字段
- HLEN key ：获取哈希表中字段的数量
- HMGET key field1 [field2] ：获取所有给定字段的值
- HMSET key field1 value1 [field2 value2] ： 同时将多个 field-value (域-值)对设置到哈希表 key 中
- HSET key field value ：同时将多个 field-value (域-值)对设置到哈希表 key 中
- HSETNX  key field value ：只有在字段 field 不存在时，设置哈希表字段的值
- HVALS key ： 获取哈希表中所有值
- HSCAN key cursor [MATCH pattern] [COUNT count] ：迭代哈希表中的键值对



**hset / hget  / hmset / hmget / hgetall / hdel** 演示

```shell
#连接Redis客户端
redis-cli
#清空数据库
flushdb
#为h1添加一个哈希键值对k1-v1
hset h1 k1 v1
#获取h1中k1的哈希值
hget h1 k1
#为h1批量添加键值对k2-v2、k3-v3
hmset h1 k2 v2 k3 v3
#批量获取h1中的k1、k2、k3
hmget h1 k1 k2 k3
#获取h1中所有字段和值
hgetall h1
#删除h1中k1的值
```

![image-20211125115953513](http://image.xiaobailx.top/images/20211125115953.png)

![image-20211125120911724](http://image.xiaobailx.top/images/20211125120911.png)



**hlen / hexists  / hsetnx**  演示

```shell
#查看h1中所有元素
hgetall h1
#获取h1中所有元素个数
hlen h1
#查看h1中是否存在k1
hexists h1 k1
#为h1中添加键值对k2-v2
hsetnx h1 k2 v2
#尝试再次为k2赋值
hsetnx h1 k2 v22
#查看h1中所有元素
hgetall h1
```

![image-20211125121823308](http://image.xiaobailx.top/images/20211125121823.png)



**hkeys / hvals** 演示

```shell
#查看h1中所有元素
hgetall h1
#获取h1中所有的键值
hkeys h1
#获取h1中所有键的值
hvals h1
```

![image-20211125122432448](http://image.xiaobailx.top/images/20211125122432.png)



**hincrby / hincrbyfloat** 

```shell
#连接Redis客户端
redis-cli
#清空数据库
flushdb
#添加h1并添加键值对k1-66
hset h1 k1 66
#为h1中的k1值添加600
hincrby h1 k1 600
#查看h1的键k1的值
hget h1 k1
#为h1中的k1值添加0.666
hincrbyfloat h1 k1 0.666
#查看h1的键k1的值
hget h1 k1
```

![image-20211125202540181](http://image.xiaobailx.top/images/20211125202540.png)



### Redis 有序集合（Zset）

- ZADD key score1 member1 [score2 member2\] ： 向有序集合添加一个或多个成员，或者更新已存在成员的分数
- ZCARD key ：获取有序集合的成员数
- ZCOUNT key min max ：计算在有序集合中指定区间分数的成员数
- ZINCRBY key increment member ：有序集合中对指定成员的分数加上增量 increment
- ZINTERSTORE destination numkeys key [key ...] ：计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 destination 中
- ZLEXCOUNT key min max ：在有序集合中计算指定字典区间内成员数量
- ZRANGE key start stop [WITHSCORES] ：通过索引区间返回有序集合指定区间内的成员
- ZRANGEBYLEX key min max [LIMIT offset count] ：通过字典区间返回有序集合的成员
- ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT] ：通过分数返回有序集合指定区间内的成员
- ZRANK key member ：返回有序集合中指定成员的索引
- ZREM key member [member ...] ：移除有序集合中的一个或多个成员
- ZREMRANGEBYLEX key min max ：移除有序集合中给定的字典区间的所有成员
- ZREMRANGEBYRANK key start stop ：移除有序集合中给定的排名区间的所有成员
- ZREMRANGEBYSCORE key min max ：移除有序集合中给定的分数区间的所有成员
- ZREVRANGE key start stop [WITHSCORES] ：返回有序集中指定区间内的成员，通过索引，分数从高到低
- ZREVRANGEBYSCORE key max min [WITHSCORES] ：返回有序集中指定分数区间内的成员，分数从高到低排序
- ZREVRANK key member ：返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序
- ZSCORE key member ：返回有序集中，成员的分数值
- ZUNIONSTORE destination numkeys key [key ...] ：计算给定的一个或多个有序集的并集，并存储在新的 key 中
- ZSCAN key cursor [MATCH pattern] [COUNT count] ：迭代有序集合中的元素（包括元素成员和元素分值）



**zadd / zrange**  演示

```shell
#清空数据库
flushdb
#添加z1并添加成员v1、v2、v3
zadd z1 10 v1 20 v2 30 v3
#通过索引获取z1的所有成员
zrange z1 0 -1
#通过索引获取z1的所有成员并按分数从高到低排序
zrange z1 0 -1 withscores
```

![image-20211125210345811](http://image.xiaobailx.top/images/20211125210346.png)



 **zrangebyscore** 演示

```shell
zrange z1 0 -1 withscores

zrangebyscore z1 10 30

zrangebyscore z1 10 30 withscores

zrangebyscore z1 (10 30 withscores

zrangebyscore z1 10 (30 withscores

zrangebyscore z1 10 +inf withscores

zrangebyscore z1 (10 +inf withscores

zrangebyscore z1 (10 +inf withscores limit 0 2

zrangebyscore z1 (10 +inf withscores limit 1 3
```

![image-20211125214627470](http://image.xiaobailx.top/images/20211125214627.png)

![image-20211125214846099](http://image.xiaobailx.top/images/20211125214846.png)

![image-20211125215040286](http://image.xiaobailx.top/images/20211125215040.png)

![image-20211125215135362](http://image.xiaobailx.top/images/20211125215135.png)



**zrem / zrevrank** 演示

```shell
#查看z1所有成员
zrange z1 0 -1
1) "v1"
2) "v2"
3) "v3"
#逆序查找z1中v1成员的下标
zrevrank z1 v1
#逆序查找z1中v3成员的下标
zrevrank z1 v3
#删除z1中v1、v2成员
zrem z1 v1 v2
```

![image-20211125212852769](http://image.xiaobailx.top/images/20211125212853.png)



**zcard / zcount / zrank / zscore**  演示

```shell
#查看z1所有成员
zrange z1 0 -1 withscores
#获取z1中分数区间在10~30的成员
zcount z1 10 30
#获取z1中v1所在下标
zrank z1 v1
#获取z1中v1所在下标
zrank z1 v2
#获取z1中v1所在下标
zrank z1 v3】
#获取z1中v1对应的分数
zscore z1 v1
#获取z1中v2对应的分数
zscore z1 v2
#获取z1中v3对应的分数
zscore z1 v3
```

![image-20211125214043562](http://image.xiaobailx.top/images/20211125214044.png)



**zrevrange / zrevrangebyscore** 演示

```shell
#通过分数获取z1的所有成员
zrange z1 0 -1 withscores
#获取z1中分数在10~20区间的成员并按分数从高到低排序
zrevrangebyscore z1 10 20 withscores
#获取z1中分数在20~10区间的成员并按分数从高到低排序
zrevrangebyscore z1 20 10 withscores
```

![image-20211125211652992](http://image.xiaobailx.top/images/20211125211653.png)

