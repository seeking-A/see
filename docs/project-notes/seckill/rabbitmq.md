---
title: RabbitMQ入门
date: 2021-12-12
tags:
 - 电商秒杀
categories:
 - Java项目
---

## RabbitMQ入门

> 以下操作基于 CentOS 7 (64-bit) 系统、MobaXterm 终端

### 安装

1. 确定兼容版本（[RabbitMQ Erlang 版本要求](https://www.rabbitmq.com/which-erlang.html)），下载	
2. 将下载至本地的 rpm 安装包上传至 Linux 服务器
3. rpm 包依赖安装

#### 1. Erlang 环境安装

RabbitMQ 由 Erlang 编写，需要环境支撑

[Erlang 官网下载地址](https://www.erlang-solutions.com/downloads/#)

![image-20220207103454699](http://image.xiaobailx.top/images/20220207103454.png)

```shell
# 安装erlang包依赖项
yum install epel-release
yum install unixODBC unixODBC-devel wxBase wxGTK SDL wxGTK-gl

# 安装erlang包
yum -y install esl-erlang_23.0.2-1_centos_7_amd64.rpm

# 检测erlang
erl
```



#### 2. 安装 RabbitMQ

[官网下载地址](http://www.rabbitmq.com/download.html)

```shell
# 安装RabbitMQ
yum -y install rabbitmq-server-3.8.5-1.el7.noarch.rpm

# 安装UI插件
rabbitmq-plugins enable rabbitmq_management

# 启用rabbitmq服务
systemctl start rabbitmq-server.service

# 检测服务
systemctl status rabbitmq-server.service
```

访问

guest用户默认只可以localhost(本机)访问

![image-20220207112255241](http://image.xiaobailx.top/images/20220207112255.png)

在rabbitmq的配置文件目录下（默认为：/etc/rabbitmq）创建一个rabbitmq.config文件。文件中添加如下配置（请不要忘记那个“.”）

```shell
[{rabbit, [{loopback_users, []}]}].
```



```shell
# 重启rabbitmq服务
systemctl restart rabbitmq-server.service
```

重新访问

![image-20220207113541030](http://image.xiaobailx.top/images/20220207113541.png)



### 控制台初识

![image-20220207114825237](http://image.xiaobailx.top/images/20220207114825.png)

![image-20220207114959478](http://image.xiaobailx.top/images/20220207114959.png)

![image-20220207115535081](http://image.xiaobailx.top/images/20220207115535.png)

![image-20220207115705976](http://image.xiaobailx.top/images/20220207115706.png)

![image-20220207120102447](http://image.xiaobailx.top/images/20220207120102.png)

![image-20220207120212893](http://image.xiaobailx.top/images/20220207120213.png)



## 集成到SpringBoot

#### 1. 引入依赖

```xml
<!-- AMQP 依赖 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```



####  2. 核心配置

```yaml
spring:
 rabbitmq:
    # 服务器地址
    host: 192.168.4.15
    # 用户名
    username: guest
    # 用户密码
    password: guest
    # 端口
    port: 5672
    listener:
      simple:
        #消费者最小数量
        concurrency: 10
        #消费者最大数量
        max-concurrency: 10
        #限制消费者每次只处理一条消息，处理完再继续下一条消息
        prefetch: 1
        #启动时是否默认启动容器，默认true
        auto-startup: true
        #被拒绝时重新进入队列
        default-requeue-rejected: true
    template:
      retry:
        #发布重试，默认false
        enabled: true
        #重试时间 默认1000ms
        initial-interval: 1000
        #重试最大次数，默认3次
        max-attempts: 3
        #重试最大间隔时间，默认10000ms
        max-interval: 10000
        #重试间隔的乘数。比如配2.0 第一次等10s，第二次等20s，第三次等40s
        multiplier: 1.0
```



####  3. 编写配置类

```java
@Configuration
public class RabbitMQConfig {

    @Bean
    public Queue queue() {
        return new Queue("queue", true);
    }
}
```



####  4. 编写 service 类

```java
/**
 * 生产者
 **/
@Service
@Slf4j
public class MQSender {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    public void send(Object msg) {
        log.info("发送消息：" + msg);
        rabbitTemplate.convertAndSend("queue", msg);
    }
}
```



```java

/**
 * 消费者
 **/
@Service
@Slf4j
public class MQReceiver {

    @RabbitListener(queues = "queue")
    public void receive(Object msg) {
        log.info("接受消息：" + msg);
    }
}
```



####  5. 编写控制器

```java
@Controller
@RequestMapping("/user")
public class UserController {

   @Autowired
   private MQSender mqSender;

   /**
    * 功能描述: 用户信息(测试)
    */
   @RequestMapping("/info")
   @ResponseBody
   public RespBean info(User user) {
      return RespBean.success(user);
   }


    /**
     * 功能描述: 测试发送RabbitMQ消息
     */
    @RequestMapping("/mq")
    @ResponseBody
    public void mq() {
      mqSender.send("Hello");
    }
}
```



控制台打印结果

```tex
com.xiaobai.seckill.rabbitmq.MQSender    : 发送消息：Hello
com.xiaobai.seckill.rabbitmq.MQReceiver  : 接受消息：(Body:'Hello' MessageProperties [headers={}, contentType=text/plain, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=, receivedRoutingKey=queue, deliveryTag=1, consumerTag=amq.ctag-9xj8PrN4llDy8zf7h-nVjw, consumerQueue=queue])
```



## Rabbit 交换机

[交换机介绍](https://www.rabbitmq.com/tutorials/tutorial-three-java.html)

> ()The core idea in the messaging model in RabbitMQ is that the producer never sends any messages directly to a queue. Actually, quite often the producer doesn't even know if a message will be delivered to any queue at all.
>
> Instead, the producer can only send messages to an *exchange*. An exchange is a very simple thing. On one side it receives messages from producers and the other side it pushes them to queues. 
>
>  The exchange must know exactly what to do with a message it receives. Should it be appended to a particular queue? Should it be appended to many queues? Or should it get discarded. The rules for that are defined by the *exchange type*.
>
> There are a few exchange types available: direct, topic, headers and fanout. 

RabbitMQ 中消息传递模型的核心思想是生产者从不直接向队列发送任何消息。生产者只能向交换机发送消息，由交换机将消息推送到队列中。

有几种可用的交换类型：direct、topic、headers 和 fanout



### Fanout 模式

[Fanout模式介绍](https://www.rabbitmq.com/tutorials/tutorial-three-java.html)

>The fanout exchange is very simple. As you can probably guess from the name, it just broadcasts all the messages it receives to all the queues it knows. And that's exactly what we need for our logger.
>
>将消息广播至所有与其绑定的队列

![fanout](http://image.xiaobailx.top/images/20220207180414.png)

#### 1. 编写配置类

```java
@Configuration
public class RabbitMQFanoutConfig {

    private static final String QUEUE01 = "queue_fanout01";
    private static final String QUEUE02 = "queue_fanout02";
    private static final String EXCHANGE = "fanoutExchange";


    @Bean
    public Queue queue01() {
        return new Queue(QUEUE01);
    }
    @Bean
    public Queue queue02() {
        return new Queue(QUEUE02);
    }

    @Bean
    public FanoutExchange fanoutExchange(){
        return new FanoutExchange(EXCHANGE);
    }

    @Bean
    public Binding binding01(){
        return BindingBuilder.bind(queue01()).to(fanoutExchange());
    }

    @Bean
    public Binding binding02(){
        return BindingBuilder.bind(queue02()).to(fanoutExchange());
    }

}

```



#### 2. 编写 service 类

```java
/**
 * 生产者
 **/
@Service
@Slf4j
public class MQSender {
    @Autowired
    private RabbitTemplate rabbitTemplate;
    
    @Autowired
    private RabbitTemplate rabbitTemplate;

    public void send(Object msg) {
        log.info("发送消息：" + msg);
        rabbitTemplate.convertAndSend("fanoutExchange", "", msg);
    }
}
```



```java
/**
 * 消费者
 **/
@Service
@Slf4j
public class MQReceiver {

    @RabbitListener(queues = "queue_fanout01")
    public void receive01(Object msg) {
        log.info("QUEUE01接受消息：" + msg);
    }

    @RabbitListener(queues = "queue_fanout02")
    public void receive02(Object msg) {
        log.info("QUEUE02接受消息：" + msg);
    }
}
```



#### 3. 编写控制器

```java
@Controller
@RequestMapping("/user")
public class UserController {
    @Autowired
	private MQSender mqSender;
    /**
    * 功能描述: Fanout模式
    */
    @RequestMapping("/mq/fanout")
    @ResponseBody
    public void mq01() {
    mqSender.send("Hello");
    }
 }
```



控制台打印结果

```tex
发送消息：Hello
QUEUE02接受消息：(Body:'Hello' MessageProperties [headers={}, contentType=text/plain, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=fanoutExchange, receivedRoutingKey=, deliveryTag=1, consumerTag=amq.ctag-RTcWnnGev4TXbFuH88uVpQ, consumerQueue=queue_fanout02])
QUEUE01接受消息：(Body:'Hello' MessageProperties [headers={}, contentType=text/plain, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=fanoutExchange, receivedRoutingKey=, deliveryTag=1, consumerTag=amq.ctag-86pqu5Ur4yoDQLIJS2PYHg, consumerQueue=queue_fanout01])
```



### Direct 模式

[Direct模式介绍](https://www.rabbitmq.com/tutorials/tutorial-four-java.html)

> The routing algorithm behind a direct exchange is simple - a message goes to the queues whose binding key exactly matches the routing key of the message.
>
> 将消息推送至与绑定键完全匹配的队列中

![direct](http://image.xiaobailx.top/images/20220207181001.png)

#### 1. 编写配置类

```java
@Configuration
public class RabbitMQRedirectConfig {

    private static final String QUEUE01 = "queue_direct01";
    private static final String QUEUE02 = "queue_direct02";
    private static final String EXCHANGE = "directExchange";
    private static final String ROUTINGKEY01 = "queue.red";
    private static final String ROUTINGKEY02 = "queue.green";


    @Bean
    public Queue queue01() {
        return new Queue(QUEUE01);
    }
    @Bean
    public Queue queue02() {
        return new Queue(QUEUE02);
    }

    @Bean
    public DirectExchange directExchange(){
        return new DirectExchange(EXCHANGE);
    }

    @Bean
    public Binding binding01(){
        return BindingBuilder.bind(queue01()).to(directExchange()).with(ROUTINGKEY01);
    }

    @Bean
    public Binding binding02(){
        return BindingBuilder.bind(queue02()).to(directExchange()).with(ROUTINGKEY02);
    }

}

```



#### 2. 编写 service 类

```java
/**
 * 生产者
 **/
@Service
@Slf4j
public class MQSender {
    @Autowired
    private RabbitTemplate rabbitTemplate;
    
    public void send01(Object msg) {
        log.info("发送消息：" + msg);
        rabbitTemplate.convertAndSend("directExchange","queue.red", msg);
    }

    public void send02(Object msg) {
        log.info("发送消息：" + msg);
        rabbitTemplate.convertAndSend("directExchange","queue.green", msg);
    }
}
```



```java
/**
 * 消费者
 **/
@Service
@Slf4j
public class MQReceiver {

    @RabbitListener(queues = "queue_direct01")
    public void receive03(Object msg) {
        log.info("QUEUE01接受消息：" + msg);
    }

    @RabbitListener(queues = "queue_direct02")
    public void receive04(Object msg) {
        log.info("QUEUE02接受消息：" + msg);
    }
}
```



#### 3. 编写控制器

```java
@Controller
@RequestMapping("/user")
public class UserController {
    @Autowired
	private MQSender mqSender;
    
	 /**
	  * 功能描述: Direct模式
	  */
	 @RequestMapping("/mq/direct01")
	 @ResponseBody
	 public void mq02() {
	 	mqSender.send01("Hello,Red");
	 }

	 /**
	  * 功能描述: Direct模式
	  */
	 @RequestMapping("/mq/direct02")
	 @ResponseBody
	 public void mq03() {
	 	mqSender.send02("Hello,Green");
	 }
 }
```



控制台打印结果

```tex
发送消息：Hello,Red
QUEUE01接受消息：(Body:'Hello,Red' MessageProperties [headers={}, contentType=text/plain, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=directExchange, receivedRoutingKey=queue.red, deliveryTag=1, consumerTag=amq.ctag-ESVvMxQPyxqE08SZf6ck0g, consumerQueue=queue_direct01])

发送消息：Hello,Green
QUEUE02接受消息：(Body:'Hello,Green' MessageProperties [headers={}, contentType=text/plain, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=directExchange, receivedRoutingKey=queue.green, deliveryTag=1, consumerTag=amq.ctag-BPIGDXD68cPeWVeK3xhReA, consumerQueue=queue_direct02])
```



### Topic 模式

[Direct模式介绍](https://www.rabbitmq.com/tutorials/tutorial-four-java.html)

> Messages sent to a topic exchange can't have an arbitrary routing_key - it must be a list of words, delimited by dots. The words can be anything, but usually they specify some features connected to the message. A few valid routing key examples: "stock.usd.nyse", "nyse.vmw", "quick.orange.rabbit". There can be as many words in the routing key as you like, up to the limit of 255 bytes.
>
> The binding key must also be in the same form. The logic behind the topic exchange is similar to a direct one - a message sent with a particular routing key will be delivered to all the queues that are bound with a matching binding key. However there are two important special cases for binding keys:
>
> - \* (star) can substitute for exactly one word.
> - \# (hash) can substitute for zero or more words.
>
> 路由键必须是单词列表，由点分隔；绑定键也必须采用相同的格式。

![topic](http://image.xiaobailx.top/images/20220207183818.png)

####  1. 编写配置类

```java
@Configuration
public class RabbitMQTopicConfig {

    private static final String QUEUE01 = "queue_topic01";
    private static final String QUEUE02 = "queue_topic02";
    private static final String EXCHANGE = "topicExchange";
    private static final String ROUTINGKEY01 = "#.queue.#";
    private static final String ROUTINGKEY02 = "*.queue.#";


    @Bean
    public Queue queue01() {
        return new Queue(QUEUE01);
    }

    @Bean
    public Queue queue02() {
        return new Queue(QUEUE02);
    }

    @Bean
    public TopicExchange topicExchange(){
        return new TopicExchange(EXCHANGE);
    }

    @Bean
    public Binding binding01(){
        return BindingBuilder.bind(queue01()).to(topicExchange()).with(ROUTINGKEY01);
    }

    @Bean
    public Binding binding02(){
        return BindingBuilder.bind(queue02()).to(topicExchange()).with(ROUTINGKEY02);
    }

}
```



####  2. 编写 service 类

```java
/**
 * 生产者
 **/
@Service
@Slf4j
public class MQSender {
    @Autowired
    private RabbitTemplate rabbitTemplate;
    
    public void send03(Object msg) {
        log.info("发送消息(被01队列接受)："+msg);
        rabbitTemplate.convertAndSend("topicExchange","queue.red.message",msg);
    }
    public void send04(Object msg) {
        log.info("发送消息(被01、02两个队列接受)："+msg);
        rabbitTemplate.convertAndSend("topicExchange","message.queue.green.abc",msg);
    }
}
```



```java
/**
 * 消费者
 **/
@Service
@Slf4j
public class MQReceiver {

    @RabbitListener(queues = "queue_topic01")
    public void receive05(Object msg) {
        log.info("QUEUE01接受消息：" + msg);
    }
    
    @RabbitListener(queues = "queue_topic02")
    public void receive06(Object msg) {
        log.info("QUEUE02接受消息：" + msg);
    }
}
```



####  3. 编写控制器

```java
@Controller
@RequestMapping("/user")
public class UserController {
    @Autowired
	private MQSender mqSender;
    
	 /**
	  * 功能描述: Topic模式
	  */
	 @RequestMapping("/mq/topic01")
	 @ResponseBody
	 public void mq04() {
	 	mqSender.send03("Hello,Red");
	 }


	 /**
	  * 功能描述: Topic模式
	  *
	  */
	 @RequestMapping("/mq/topic02")
	 @ResponseBody
	 public void mq05() {
	 	mqSender.send04("Hello,Green");
	 }

 }
```



控制台打印结果

```tex
发送消息(被01队列接受)：Hello,Red
QUEUE01接受消息：(Body:'Hello,Red' MessageProperties [headers={}, contentType=text/plain, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=topicExchange, receivedRoutingKey=queue.red.message, deliveryTag=1, consumerTag=amq.ctag-rpL8whwREQEo2keskQAFXQ, consumerQueue=queue_topic01])

发送消息(被01、02两个队列接受)：Hello,Green
QUEUE01接受消息：(Body:'Hello,Green' MessageProperties [headers={}, contentType=text/plain, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=topicExchange, receivedRoutingKey=message.queue.green.abc, deliveryTag=1, consumerTag=amq.ctag-C6baDbYW51RTC_s6LHabeA, consumerQueue=queue_topic01])
QUEUE02接受消息：(Body:'Hello,Green' MessageProperties [headers={}, contentType=text/plain, contentEncoding=UTF-8, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=topicExchange, receivedRoutingKey=message.queue.green.abc, deliveryTag=1, consumerTag=amq.ctag-BE9W2NXLQIh3g7UCQHOU5Q, consumerQueue=queue_topic02])

```



### Headers 模式

> 通过 headers 键值对方式匹配队列，而非路由键。性能较低，实际应用地较少，学习作为了解知识点。

#### 1. 编写配置类

```java
@Configuration
public class RabbitMQHeadersConfig {

    private static final String QUEUE01 = "queue_header01";
    private static final String QUEUE02 = "queue_header02";
    private static final String EXCHANGE = "headersExchange";


    @Bean
    public Queue queue01() {
        return new Queue(QUEUE01);
    }

    @Bean
    public Queue queue02() {
        return new Queue(QUEUE02);
    }

    @Bean
    public HeadersExchange headerExchange(){
        return new HeadersExchange(EXCHANGE);
    }

    @Bean
    public Binding binding01(){
        Map<String,Object> map = new HashMap<>();
        map.put("color","red");
        map.put("speed","low");
        return BindingBuilder.bind(queue01()).to(headerExchange()).whereAny(map).match();
    }

    @Bean
    public Binding binding02(){
        Map<String,Object> map = new HashMap<>();
        map.put("color","red");
        map.put("speed","fast");
        return BindingBuilder.bind(queue01()).to(headerExchange()).whereAny(map).match();
    }

}
```



#### 2. 编写 service 类

```java
/**
 * 生产者
 **/
@Service
@Slf4j
public class MQSender {
    @Autowired
    private RabbitTemplate rabbitTemplate;
    public void send05(String msg) {
        log.info("发送消息(被01队列接受)：" + msg);
        MessageProperties properties = new MessageProperties();
        properties.setHeader("color", "red");
        properties.setHeader("speed", "normal");
        Message message = new Message(msg.getBytes(), properties);
        rabbitTemplate.convertAndSend("headersExchange", "", message);
    }

    public void send06(String msg) {
        log.info("发送消息(被01、02两个队列接受)：" + msg);
        MessageProperties properties = new MessageProperties();
        properties.setHeader("color", "red");
        properties.setHeader("speed", "fast");
        Message message = new Message(msg.getBytes(), properties);
        rabbitTemplate.convertAndSend("headersExchange", "", message);
    }
}
```



```java
/**
 * 消费者
 **/
@Service
@Slf4j
public class MQReceiver {

   @RabbitListener(queues = "queue_header01")
    public void receive01(Message message) {
        log.info("QUEUE01接受Message对象：" + message);
        log.info("QUEUE01接受消息：" + new String(message.getBody()));
    }

    @RabbitListener(queues = "queue_header02")
    public void receive02(Message message) {
        log.info("QUEUE02接受Message对象：" + message);
        log.info("QUEUE02接受消息：" + new String(message.getBody()));
    }
}
```



#### 3. 编写控制器

```java
@Controller
@RequestMapping("/user")
public class UserController {
    @Autowired
	private MQSender mqSender;
    
	 /**
	  * 功能描述: Header模式
	  */
	 @RequestMapping("/mq/header01")
	 @ResponseBody
	 public void mq06() {
	 	mqSender.send05("Hello,Header01");
	 }

	 /**
	  * 功能描述: Header模式
	  *
	  */
	 @RequestMapping("/mq/header02")
	 @ResponseBody
	 public void mq07() {
	 	mqSender.send06("Hello,Header02");
	 }

 }
```



控制台打印结果

```tex
发送消息(被01队列接受)：Hello,Header01
QUEUE01接受Message对象：(Body:'[B@6b963100(byte[14])' MessageProperties [headers={color=red, speed=normal}, contentType=application/octet-stream, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=headersExchange, receivedRoutingKey=, deliveryTag=1, consumerTag=amq.ctag-cHqi-JI_iXyYsS9Uw5RxXQ, consumerQueue=queue_header01])
QUEUE01接受消息：Hello,Header01

发送消息(被01、02两个队列接受)：Hello,Header02
QUEUE01接受Message对象：(Body:'[B@155d2639(byte[14])' MessageProperties [headers={color=red, speed=fast}, contentType=application/octet-stream, contentLength=0, receivedDeliveryMode=PERSISTENT, priority=0, redelivered=false, receivedExchange=headersExchange, receivedRoutingKey=, deliveryTag=1, consumerTag=amq.ctag-vBv_ZKQOuayeG1LJSBbnkw, consumerQueue=queue_header01])
QUEUE01接受消息：Hello,Header02

```

 

