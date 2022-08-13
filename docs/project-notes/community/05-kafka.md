---
title: Kafka入门
date: 2022-01-16
tags:
 - 仿牛客社区
categories:
 - Java项目
---

## 阻塞队列

![image-20220214195149086](http://image.xiaobailx.top/images/20220214195149.png)

详见：[阻塞队列](https://www.yuque.com/docs/share/fcc5f811-a7ec-41d6-a6ea-c538cee252ef?# 《09-阻塞队列》)



## KafKa入门

![image-20220214200000678](http://image.xiaobailx.top/images/20220214200000.png)

### Kafka 术语

Broker：Kafka集群包含一个或多个服务器，这种服务器被称为broker。

Zookeeper：独立的集群管理工具，Kafka 内置该工具。

Topic：每条发布到Kafka集群的消息都有一个类别，这个类别被称为Topic。

Partition：Partition是物理上的概念，每个Topic包含一个或多个Partition。

Leader Replica：

Fllower Replica：

[官方使用文档](https://kafka.apache.org/documentation/)



## Kafka 安装使用

### 下载

[官网下载](https://kafka.apache.org/downloads)

这里选择与课程一致版本——[kafka_2.12-2.3.0.tgz](https://archive.apache.org/dist/kafka/2.3.0/kafka_2.12-2.3.0.tgz) ([asc](https://archive.apache.org/dist/kafka/2.3.0/kafka_2.12-2.3.0.tgz.asc), [sha512](https://archive.apache.org/dist/kafka/2.3.0/kafka_2.12-2.3.0.tgz.sha512))，下载解压。

### 配置

修改 config 目录下的 zookeeper.proertites 文件

```properties
#修改存放数据路径
dataDir=e:/data/zookeeper
```

修改 config 目录下的 server.propertities 文件

```properties
#修改存放数据路径
log.dirs=e:/data/kafka-logs
```



### 启动运行

[快速开始](https://kafka.apache.org/quickstart)

```shell

cd D:\Devware\kafka_2.12-2.3.1
# 启动zookeeper服务
bin\windows\zookeeper-server-start.bat config\zookeeper.properties
# 启动kafaka服务
bin\windows\kafka-server-start.bat config\server.properties
# 创建主题test
bin\windows\kafka-topics.bat --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic test
# 查看主题列表
bin\windows\kafka-topics.bat --list --bootstrap-server localhost:9092
# 生产者发送消息
bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic test
# 消费者接收消息
bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic test --from-beginning
```



## SpringBoot整合

### 1. 引入依赖

```xml
<dependency>
   <groupId>org.springframework.kafka</groupId>
   <artifactId>spring-kafka</artifactId>
</dependency>
```



### 2. Kafka配置

```properties
spring.kafka.bootstrap-servers=localhost:9092
# 与config目录下的cusumer.properties文件中的group.id一致
spring.kafka.consumer.group-id=community-consumer-group
spring.kafka.consumer.enable-auto-commit=true
# 自动提交频率
spring.kafka.consumer.auto-commit-interval=3000
```



### 3.测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@ContextConfiguration(classes = CommunityApplication.class)
public class KafkaTests {

    @Autowired
    private KafkaProducer kafkaProducer;

    @Test
    public void testKafka() {
        kafkaProducer.sendMessage("test", "你好");
        kafkaProducer.sendMessage("test", "在吗");

        try {
            Thread.sleep(1000 * 10);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

}

@Component
class KafkaProducer {

    @Autowired
    private KafkaTemplate kafkaTemplate;

    public void sendMessage(String topic, String content) {
        kafkaTemplate.send(topic, content);
    }

}

@Component
class KafkaConsumer {

    @KafkaListener(topics = {"test"})
    public void handleMessage(ConsumerRecord record) {
        System.out.println(record.value());
    }


}
```
