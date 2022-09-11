---
title: SpringCloud Alibaba
date: 2022-09-11
tags:
 - SpringCloud
categories:
 - Java框架
---

# SpringCloud Alibaba入门简介

Spring Cloud Netflix 项目进入维护模式，Spring Cloud Netflix 将不再开发新的组件。Spring Cloud 版本迭代算是比较快的，因而出现了很多重大 ISSUE 都还来不及 Fix 就又推另一个 Release 了。进入维护模式意思就是目前一直以后一段时间Spring Cloud Netflix提供的服务和功能就这么多了，不在开发新的组件和功能了。以后将以维护和Merge分支Full Request为主，新组件功能将以其他替代平代替的方式实现。基于该背景下，诞生了 SpringCloud Alibaba.

官网：https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md



## SpringCloud Alibaba 特性

1. 服务限流降级：默认支持 Servlet、Feign、RestTemplate、Dubbo 和 RocketMQ 限流降级功能的接入，可以在运行时通过控制台实时修改限流降级规则，还支持查看限流降级 Metrics 监控。
2. 服务注册与发现：适配 Spring Cloud 服务注册与发现标准，默认集成了 Ribbon 的支持。
3. 分布式配置管理：支持分布式系统中的外部化配置，配置更改时自动刷新。
4. 消息驱动能力：基于 Spring Cloud Stream 为微服务应用构建消息驱动能力。
5. 阿里云对象存储：阿里云提供的海量、安全、低成本、高可靠的云存储服务。支持在任何应用、任何时间、任何地点存储和访问任意类型的数据。
6. 分布式任务调度：提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。同时提供分布式的任务执行模型，如网格任务。网格任务支持海量子任务均匀分配到所有 Worker（schedulerx-client）上执行。



## Spring Alibaba核心组件

![image-20220821151343839](http://image.xiaobailx.top/images/202208211513191.png)

官网：https://spring.io/projects/spring-cloud-alibaba#overview

英文文档：https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html

中文文档：https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md

# Nacos服务注册和配置中心

## Nacos简介

官网：https://nacos.io/zh-cn/index.html

GitHub：https://github.com/alibaba/Nacos

开发手册：https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html

Nacos，全称 Dynamic Naming and Configuration Service，Nacos = Eureka+Config +Bus，能够替代 Eureka 做服务注册中心和 Config 做服务配置中心。

**各类注册中心比较**

| 服务注册与发现框架 | CAP模型 | 控制台管理 | 社区活跃度     |
| ------------------ | ------- | ---------- | -------------- |
| Euraka             | AP      | 支持       | 低（2.x 闭源） |
| Zookeeper          | CP      | 不支持     | 中             |
| Consul             | CP      | 支持       | 高             |
| Nacos              | AP      | 支持       | 高             |



## Nacos安装与运行

1. 准备 Java8 + Maven 环境
2. 下载 Nocas：https://github.com/alibaba/nacos/releases
3. 解压安装包，直接运行 bin 目录下的 startup.cmd

```shell
startup.cmd -m standalone
```

4. 命令运行成功后直接访问：http://localhost:8848/nacos



## Nacos服务注册中心

### 基于Nacos的服务提供者

1. 新建 module：cloudalibaba-provider-payment9001

2. 改 POM

   - 修改父 POM，添加以下依赖

   ```xml
   <dependency>
       <groupId>com.alibaba.cloud</groupId>
       <artifactId>spring-cloud-alibaba-dependencies</artifactId>
       <version>2.1.0.RELEASE</version>
       <type>pom</type>
       <scope>import</scope>
   </dependency>
   ```

   - 本模块 POM

   ```xml
   <dependencies>
           <!--SpringCloud ailibaba nacos -->
           <dependency>
               <groupId>com.alibaba.cloud</groupId>
               <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
           </dependency>
           <!-- SpringBoot整合Web组件 -->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-actuator</artifactId>
           </dependency>
           <!--日常通用jar包配置-->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-devtools</artifactId>
               <scope>runtime</scope>
               <optional>true</optional>
           </dependency>
           <dependency>
               <groupId>org.projectlombok</groupId>
               <artifactId>lombok</artifactId>
               <optional>true</optional>
           </dependency>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-test</artifactId>
               <scope>test</scope>
           </dependency>
       </dependencies>
   ```

3. 写 YML

```yaml
server:
  port: 9001

spring:
  application:
    name: nacos-payment-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos地址

management:
  endpoints:
    web:
      exposure:
        include: '*'
```

4. 主启动

```java
@EnableDiscoveryClient
@SpringBootApplication
public class PaymentMain9001
{
    public static void main(String[] args) {
            SpringApplication.run(PaymentMain9001.class, args);
    }
}
```

5. 业务类

```java
@RestController
public class PaymentController
{
    @Value("${server.port}")
    private String serverPort;

    @GetMapping(value = "/payment/nacos/{id}")
    public String getPayment(@PathVariable("id") Integer id)
    {
        return "nacos registry, serverPort: "+ serverPort+"\t id"+id;
    }
}
```

6. 测试
   - 访问：http://localhost:9001/payment/nacos/1，控制台输出服务注册成功提示；
   - 访问 Nacos 控制台：http://localhost:8848/nacos，服务列表中显示注册成功的服务提供者 



### 基于Nacos的服务消费者

1. 根据 cloudalibaba-provider-payment9001 新建 cloudalibaba-provider-payment9002 演示负载均衡。
2. 新建 module：cloudalibaba-consumer-nacos-order83
3. 改 POM

```xml
<dependencies>
        <!--SpringCloud ailibaba nacos -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <dependency>
            <groupId>com.xiaobai.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--日常通用jar包配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

4. 写 YML

```yaml
server:
  port: 83


spring:
  application:
    name: nacos-order-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848


#消费者将要去访问的微服务名称(注册成功进nacos的微服务提供者)
service-url:
  nacos-user-service: http://nacos-payment-provider 
```

5. 主启动

```java
@EnableDiscoveryClient
@SpringBootApplication
public class OrderNacosMain83
{
    public static void main(String[] args)
    {
        SpringApplication.run(OrderNacosMain83.class,args);
    }
} 
```

6. 业务类

   - config

   ```java
   @Configuration
   public class ApplicationContextBean
   {
       @Bean
       @LoadBalanced
       public RestTemplate getRestTemplate()
       {
           return new RestTemplate();
       }
   }
   ```

   - OrderNacosController

   ```java
   @RestController
   public class OrderNacosController
   {
       @Resource
       private RestTemplate restTemplate;
   
       @Value("${service-url.nacos-user-service}")
       private String serverURL;
   
       @GetMapping("/consumer/payment/nacos/{id}")
       public String paymentInfo(@PathVariable("id") Long id)
       {
           return restTemplate.getForObject(serverURL+"/payment/nacos/"+id,String.class);
       }
   }
   ```

7. 测试
   - 启动 Nocas 服务、服务提供者 9001、9002
   - 访问：http://localhost:83/consumer/payment/nacos/13，实现 83 访问 9001/9002，轮询负载OK

**为什么 Nocas 支持负载轮询?** Nocas 中内置了 Ribbon ！

![image-20220822222110638](http://image.xiaobailx.top/images/202208222221239.png)



### 服务注册中心对比

**Nocas 全景图**

![image-20220822222303786](http://image.xiaobailx.top/images/202208222223942.png)



**Nacos与其他注册中心特性对比**

|                 | Nacos                      | Euraka      | Consul            | CoreDNS | ZooKeeper   |
| --------------- | -------------------------- | ----------- | ----------------- | ------- | ----------- |
| 一致性协议      | CP + AP                    | AP          | CP                | /       | CP          |
| 健康检查        | TCP/HTTP/MySQL/Client Beat | Client Beat | TCP/HTTP/GRPC/Cmd | /       | Client Beat |
| 负载均衡        | 权重/DSL/metadata/CMDB     | Ribbon      | Fabio             | RR      | /           |
| 雪崩保护        | 支持                       | 支持        | 不支持            | 不支持  | 不支持      |
| 自动注销        | 支持                       | 支持        | 不支持            | 不支持  | 不支持      |
| 访问协议        | HTTP/DNS/UDP               | HTTP        | HTTP/DNS          | DNS     | TCP         |
| 监听支持        | 支持                       | 支持        | 支持              | 不支持  | 支持        |
| 多数据中心      | 支持                       | 支持        | 支持              | 不支持  | 不支持      |
| 跨注册中心      | 支持                       | 不支持      | 支持              | 不支持  | 不支持      |
| SpringCloud集成 | 支持                       | 不支持      | 不支持            | 不支持  | 支持        |
| Dubbon集成      | 支持                       | 不支持      | 不支持            | 不支持  | 支持        |
| K8s集成         | 支持                       | 不支持      | 支持              | 支持    | 不支持      |



**Nacos 服务发现实例模型**


![](http://image.xiaobailx.top/images/202208222230386.png)





**Nacos 支持AP和CP模式的切换**

C 是所有节点在同一时间看到的数据是一致的；而 A 的定义是所有的请求都会收到响应。

**何时选择使用何种模式？**

一般来说，如果不需要存储服务级别的信息且服务实例是通过nacos-client注册，并能够保持心跳上报，那么就可以选择AP模式。当前主流的服务如 Spring cloud 和 Dubbo 服务，都适用于AP模式，AP模式为了服务的可能性而减弱了一致性，因此AP模式下只支持注册临时实例。

如果需要在服务级别编辑或者存储配置信息，那么 CP 是必须，K8S服务和DNS服务则适用于CP模式。
CP模式下则支持注册持久化实例，此时则是以 Raft 协议为集群运行模式，该模式下注册实例之前必须先注册服务，如果服务不存在，则会返回错误。

```shell
curl -X PUT '$NACOS_SERVER:8848/nacos/v1/ns/operator/switches?entry=serverMode&value=CP'
```



## Nacos服务配置中心

### 基础配置

1. 新建 module：cloudalibaba-config-nacos-client3377
2. 改 POM 

```xml
<dependencies>
        <!--nacos-config-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>
        <!--nacos-discovery-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!--web + actuator-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--一般基础配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

3. 写 YML

   - bootstrap

   ```yaml
   # nacos配置
   server:
     port: 3377
   
   spring:
     application:
       name: nacos-config-client
     cloud:
       nacos:
         discovery:
           server-addr: localhost:8848 #Nacos服务注册中心地址
         config:
           server-addr: localhost:8848 #Nacos作为配置中心地址
           file-extension: yaml #指定yaml格式的配置
    
    
   # ${spring.application.name}-${spring.profile.active}.${spring.cloud.nacos.config.file-extension}
   ```

   - application

   ```yaml
   spring:
     profiles:
       active: dev # 表示开发环境
   ```

4. 主启动

```java
@EnableDiscoveryClient
@SpringBootApplication
public class NacosConfigClientMain3377
{
    public static void main(String[] args) {
            SpringApplication.run(NacosConfigClientMain3377.class, args);
    }
}
```

5. 业务类

   - controller

   ```java
   @RestController //通过Spring Cloud原生注解@RefreshScope实现配置自动更新
   @RefreshScope //在控制器类加入@RefreshScope注解使当前类下的配置支持Nacos的动态刷新功能。
   public class ConfigClientController
   {
       @Value("${config.info}")
       private String configInfo;
   
       @GetMapping("/config/info")
       public String getConfigInfo() {
           return configInfo;
       }
   }
   ```

6. 在 Nacos 中添加配置信息

   > Nacos中的匹配规则：https://nacos.io/zh-cn/docs/quick-start-spring-cloud.html
   >
   > 设置DataId：${spring.application.name}-${spring.profiles.active}.${spring.cloud.nacos.config.file-extension}
   >
   > - prefix 默认为 spring.application.name 的值；
   > - spring.profile.active 即为当前环境对应的 profile，可以通过配置项 spring.profile.active 来配置；
   > - file-exetension 为配置内容的数据格式，可以通过配置项 spring.cloud.nacos.config.file-extension 来配置
   >
   > Nacos 会记录配置文件的历史版本默认保留30天，此外还有一键回滚功能，回滚操作将会触发配置更新。

![image-20220822225618049](http://image.xiaobailx.top/images/202208222256135.png)

7. 测试
   - 启动前需要在nacos客户端-配置管理-配置管理栏目下有对应的yaml配置文件；
   - 运行cloud-config-nacos-client3377的主启动类；
   - 调用接口查看配置信息：http://localhost:3377/config/info
   - 修改下 Nacos 中的yaml配置文件，再次调用查看配置的接口，就会发现配置已经刷新



### 分类配置

**多环境多项目管理中面临的问题：**

1. 实际开发中，通常一个系统会准备 dev开发环境、test测试环境、prod生产环境。如何保证指定环境启动时服务能正确读取到Nacos上相应环境的配置文件呢？
2. 一个大型分布式微服务系统会有很多微服务子项目，每个微服务项目又都会有相应的开发环境、测试环境、预发环境、正式环境......
   那怎么对这些微服务配置进行管理呢？

**Nacos 的图形化管理界面**

![image-20220828193156061](http://image.xiaobailx.top/images/202208281931185.png)

**Namespace+Group+Data ID三者关系**

![image-20220828193307419](http://image.xiaobailx.top/images/202208281933501.png)

默认情况：Namespace=public，Group=DEFAULT_GROUP, 默认Cluster是DEFAULT

Nacos 默认的命名空间是 public，Namespace 主要用来实现隔离。比方说我们现在有三个环境：开发、测试、生产环境，我们就可以创建三个Namespace，不同的Namespace之间是隔离的。

Group 默认是 DEFAULT_GROUP，Group 可以把不同的微服务划分到同一个分组里面去。

Service 就是微服务；一个Service可以包含多个Cluster（集群），Nacos 默认 Cluster 是 DEFAULT，Cluster 是对指定微服务的一个虚拟划分。比方说为了容灾，将 Service 微服务分别部署在了杭州机房和广州机房，这时就可以给杭州机房的 Service 微服务起一个集群名称（HZ），给广州机房的Service微服务起一个集群名称（GZ），还可以尽量让同一个机房的微服务互相调用，以提升性能。

最后是 Instance，就是微服务的实例。

**三种方案加载配置**

- DataID方案：指定spring.profile.active和配置文件的DataID来使不同环境下读取不同的配置。

  1. 新建 dev 配置 DataID

  ![image-20220828194145448](http://image.xiaobailx.top/images/202208281941525.png)

  2. 同上，新建 test 配置 DataID
  3. 通过spring.profile.active属性就能进行多环境下配置文件的读取

  ![image-20220828194351544](http://image.xiaobailx.top/images/202208281943632.png)

  4. 测试，访问：http://localhost:3377/config/info，返回配置内容

- Group方案：通过Group实现环境区分

  1. 新建 Group，在 nacos 图形界面控制台上面新建配置文件DataID

  ![image-20220828194558137](http://image.xiaobailx.top/images/202208281945215.png)

  2. 在config下增加一条group的配置即可。可配置为 DEV_GROUP 或 TEST_GROUP

  ![image-20220828195710576](http://image.xiaobailx.top/images/202208281957664.png)

  3. 测试，访问：http://localhost:3377/config/info，返回配置内容

- Namespace方案

  1. 新建dev/test的Namespace

  ![image-20220828202442040](http://image.xiaobailx.top/images/202208282024124.png)

  2. 回到服务管理-服务列表查看

  ![image-20220828202510990](http://image.xiaobailx.top/images/202208282025062.png)

  3. 按照域名配置填写

  ![image-20220828202544924](http://image.xiaobailx.top/images/202208282025013.png)

  4. 修改 YML

     - bootstrap：config 添加 namespace 配置

     ```yaml
     config:
             server-addr: localhost:8848 #Nacos作为配置中心地址
             file-extension: yaml #这里我们获取的yaml格式的配置
             namespace: 5da1dccc-ee26-49e0-b8e5-7d9559b95ab0
             #group: DEV_GROUP
             group: TEST_GROUP
     ```

     - application

     ```yaml
     # Nacos注册配置，application.yml
     spring:
       profiles:
         #active: test
         active: dev
         #active: info
     ```

  5. 测试，访问：http://localhost:3377/config/info，返回配置内容



## Nacos集群和持久化配置

### 官网说明

官方文档：https://nacos.io/zh-cn/docs/cluster-mode-quick-start.html

![image-20220828211246075](http://image.xiaobailx.top/images/202208282112153.png)

默认Nacos使用嵌入式数据库实现数据的存储。所以，如果启动多个默认配置下的Nacos节点，数据存储是存在一致性问题的。
为了解决这个问题，Nacos采用了集中式存储的方式来支持集群化部署，目前只支持MySQL的存储。

![image-20220828211542707](http://image.xiaobailx.top/images/202208282115785.png)

![image-20220828211638243](http://image.xiaobailx.top/images/202208282116346.png)

> 查看官网文档说明：https://nacos.io/zh-cn/docs/deployment.html



### Nacos持久化配置

Nacos默认自带的是嵌入式数据库derby，说明：https://github.com/alibaba/nacos/blob/develop/config/pom.xml

**切换配置 MySQL 步骤**

1. 新建 nacos 数据库，在 nacos-server-1.1.4\nacos\conf 目录下找到 sql 脚本，执行：

```shell
create database nacos;
use nacos;
source D:\Devware\nacos-server-2.1.0-BETA\conf\nacos-mysql.sql
```

2. nacos-server-1.1.4\nacos\conf 目录下找到 application.properties，修改数据库配置

```shell
#*************** Config Module Related Configurations ***************#
### If use MySQL as datasource:
spring.datasource.platform=mysql

### Count of DB:
db.num=1

### Connect URL of DB:
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user.0=root
db.password.0=root
```

3. 启动Nacos，可以看到是个全新的空记录界面，以前是记录进derby



### Linux版Nacos+MySQL、生产环境配置

1. 环境准备

   - 下载 Linux 版 Nacos： https://github.com/alibaba/nacos/releases/tag/1.1.4，解压到 opt 目录下

   ```shell
   tar -xzvf /opt/ nacos-server-1.1.4.tar.gz
   ```

   

2. 集群配置

   -  新建 nacos 数据库，将 nacos 安装目录里的 nacos-mysql.sql 导入到 mysql 数据库中

   ```sql
   create database nacos;
   use nacos;
   source /opt/nacos/confnacos-mysql.sql
   ```

   - 修改 application.properties 配置

   ```shell
   cp application.properties.example application.properties
   vim application.properties
   ```

   ![image-20220830214401415](http://image.xiaobailx.top/images/202208302144388.png)

   - Linux服务器上nacos的集群配置 cluster.conf

   ```shell
   cp cluster.conf.example cluster.conf
   vim cluster.conf
   ```

   ![image-20220901221836125](C:\Users\20689\AppData\Roaming\Typora\typora-user-images\image-20220901221836125.png)

   - 编辑Nacos的启动脚本 startup.sh，使它能够接受不同的启动端口

   ```shell
   cd /opt/nacos/bin
   vim startup.sh
   ```

   ![image-20220830215410645](http://image.xiaobailx.top/images/202208302154718.png)

   ![image-20220902002128610](http://image.xiaobailx.top/images/202209020021743.png)

   ```shell
   #执行方式
   ./startup.sh -p 3333
   ./startup.sh -p 4444
   ```

   - Nginx的配置，由它作为负载均衡器

   ```shell
   vim /usr/local/nginx/conf
   ```

   配置内容

   ```shell
   upstream cluster{
       server 127.0.0.1:3333;
       server 127.0.0.1:4444;
       server 127.0.0.1:5555;
   }
   
   server {
       listen       1111;
       server_name  localhost;
       #charset koi8-r;
       #access_log  logs/host.access.log  main;
       location / {
       #root   html;
       #index  index.html index.htm;
       proxy_pass http://cluster;
   }
   ......
   ```

   启动 nginx，执行： ./nginx -C /usr/local/nginx/conf/nginx.conf

   - 截止到此处，1个Nginx+3个nacos注册中心+1个mysql

     测试通过nginx访问nacos ：http://192.168.4.15:1111/nacos/#/login

     新建一个测试配置

     ![image-20220830220130928](http://image.xiaobailx.top/images/202208302201995.png)

     服务器插入一条数据

     ![image-20220830220212197](http://image.xiaobailx.top/images/202208302202258.png)

3. 测试

   - 修改 cloudablibaba-provider-payment9002 yml
   ```yaml
   server-addr: 192.168.111.144:1111
   ```
   - 微服务 cloudalibaba-provider-payment9002 启动，注册进 nacos 集群

   ![image-20220830220420634](http://image.xiaobailx.top/images/202208302204706.png)

# Sentinel实现熔断与限流

## Sentinel简介

GitHub：https://github.com/alibaba/Sentinel

[Sentinel](https://sentinelguard.io/) 是一款功能强大的流量控制组件，以 flow 为突破点，覆盖流量控制、并发限制、断路、自适应系统保护等多个领域，保障微服务的可靠性。

![image-20220824195439848](http://image.xiaobailx.top/images/202208241954260.png)

使用手册：https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html#_spring_cloud_alibaba_sentinel



## Sentinel安装

Sentinel 分为两个部分：

- 核心库(Uava客户端)：不依赖任何框架/库，能够运行于所有ava运行时环境，同时对 Dubbo/Spring Cloud 等框架也有较好的支特。
- 控制台(Dashboard)：基于Spring Boot开发，打包后可以直接运行，不需要额外的 Tomcat 等应用容器。

1. 下载：https://github.com/alibaba/Sentinel/releases
2.  保证 Java8 环境且 8080 端口不被占用，运行 `java -jar sentinel-dashboard-1.7.0.jar`
3. 访问sentinel管理界面：http://localhost:8080 ，登录账号密码均为 sentinel



## 初始化工程

1. 新建 module，cloudalibaba-sentinel-service8401
2. 改 POM 

```xml
<dependencies>
        <!--SpringCloud ailibaba nacos -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!--SpringCloud ailibaba sentinel-datasource-nacos 后续做持久化用到-->
        <dependency>
            <groupId>com.alibaba.csp</groupId>
            <artifactId>sentinel-datasource-nacos</artifactId>
        </dependency>
        <!--SpringCloud ailibaba sentinel -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
        </dependency>
        <!--openfeign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!-- SpringBoot整合Web组件+actuator -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--日常通用jar包配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>4.6.3</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

    </dependencies>
```

3. 写 YML

```yaml
server:
  port: 8401

spring:
  application:
    name: cloudalibaba-sentinel-service
  cloud:
    nacos:
      discovery:
        #Nacos服务注册中心地址
        server-addr: localhost:8848
    sentinel:
      transport:
        #配置Sentinel dashboard地址
        dashboard: localhost:8080
        #默认8719端口，假如被占用会自动从8719开始依次+1扫描,直至找到未被占用的端口
        port: 8719

management:
  endpoints:
    web:
      exposure:
        include: '*'
```

4. 主启动

```java
@EnableDiscoveryClient
@SpringBootApplication
public class MainApp8401
{
    public static void main(String[] args) {
        SpringApplication.run(MainApp8401.class, args);
    }
}
```

5. controller

```java
@RestController
@Log4j2
public class FlowLimitController
{

    @GetMapping("/testA")
    public String testA()
    {
        return "------testA";
    }

    @GetMapping("/testB")
    public String testB()
    {
        return "------testB";
    }
}
```

6. 测试

   - 启动 nacos 执行 `startup.cmd -m standalone`，访问：http://localhost:8848/nacos/#/login
   - 启动 sentinel，执行 `java -jar sentinel-dashboard-1.7.0.jar`，访问：http://localhost:8080
   - 启动8401微服务，查看 sentienl 控制台，分别访问：http://localhost:8401/testA、http://localhost:8401/testB
   

![image-20220824201256133](http://image.xiaobailx.top/images/202208242012202.png)

> Sentinel 采用的懒加载方式，只有在微服务被访问之后 Sentienl 才进行监测。



## 流控规则

![image-20220824201540069](http://image.xiaobailx.top/images/202208242015138.png)

- 资源名：唯一名称，默认请求路径
- 针对来源：Sentinel可以针对调用者进行限流，填写微服务名，默认default（不区分来源）
- 调值类型单机阔值：
  - QPS（每秒钟的请求数量）：当调用该 API 的 QPS 达到阈值的时候，进行限流
  - 线程数：当调用该p的线程数达到阔值的时候，进行限流
- 是否集群：不需要集群
- 流控模式：
  - 直接：API 达到限流条件时，直接限流
  - 关联：当关联的资源达到阈值时，就限流自己
- 链路：只记录指定链路上的流量(指定资源从入口资源进来的流量，如果达到城值，就进行限流)【 API 级别的针对来源】
- 流控效果：
  - 快速失败：直接失败，抛异常
  - warm Up:根据 codeFactor （冷加载因子，默认3) 的值，从阔值/codeFactor,经过预热时长，才达到设置的QPS阔值
  - 排队等待：匀速排队，让请求以匀速的速度通过，阈值类型必须设置为QPS,否则无效

**流量模式**

- 直接模式（默认）：直接->快速失败

![image-20220824205931469](http://image.xiaobailx.top/images/202208242059523.png)

> 表示1秒钟内查询1次就是OK，若超过次数1，就直接-快速失败，报默认错误。

​	快速点击访问：http://localhost:8401/testA，结果 Blocked by Sentinel (flow limiting)

- 关联模式：当关联的资源达到阈值时，就限流自己

![image-20220828111029179](http://image.xiaobailx.top/images/202208281110295.png)

postman 模拟并发密集访问 testB，大批量线程高并发访问B，导致A失效了

![image-20220824210538330](http://image.xiaobailx.top/images/202208242105412.png)

- 链路模式：多个请求调用了同一个微服务

**流控效果**

- 默认的流控处理：直接->快速失败

  源码：com.alibaba.csp.sentinel.slots.block.flow.controller.DefaultController

- 预热：阈值除以coldFactor(默认值为3),经过预热时长后才会达到阈值

  ![image-20220824211142224](http://image.xiaobailx.top/images/202208242111286.png)

> 文档：https://github.com/alibaba/Sentinel/wiki/%E6%B5%81%E9%87%8F%E6%8E%A7%E5%88%B6
>
> 源码：com.alibaba.csp.sentinel.slots.block.flow.controller.WarmUpController

  例如：系统初始化的阀值为10 / 3 约等于3,即阀值刚开始为3；然后过了5秒后阀值才慢慢升高恢复到10

![image-20220824211315336](http://image.xiaobailx.top/images/202208242113395.png)

  测试：多次点击：http://localhost:8401/testB，刚开始不行，后续慢慢OK

  运用场景如：秒杀系统在开启的瞬间，会有很多流量上来，很有可能把系统打死，预热方式就是把为了保护系统，可慢慢的把流量放进来，慢慢的把阀值增长到设置的阀值。

- 等待排队：匀速排队，让请求以均匀的速度通过，阀值类型必须设成QPS，否则无效。

> 官网：https://github.com/alibaba/Sentinel/wiki/%E6%B5%81%E9%87%8F%E6%8E%A7%E5%88%B6
>
> 源码：com.alibaba.csp.sentinel.slots.block.flow.controller.RateLimiterController

  测试

![image-20220824211757086](http://image.xiaobailx.top/images/202208242117152.png)



## 降级规则

官网：https://github.com/alibaba/Sentinel/wiki/%E7%86%94%E6%96%AD%E9%99%8D%E7%BA%A7

Sentinel 熔断降级会在调用链路中某个资源出现不稳定状态时（例如调用超时或异常比例升高），对这个资源的调用进行限制，
让请求快速失败，避免影响到其它的资源而导致级联错误。

当资源被降级后，在接下来的降级时间窗口之内，对该资源的调用都自动熔断（默认行为是抛出 DegradeException）。

注意：**Sentinel的断路器是没有半开状态的**

![image-20220824212037295](http://image.xiaobailx.top/images/202208242120343.png)

**降级策略实战**

1. RT（平均响应时间，秒级）：平均响应时间 **超出阈值** 且 **在时间窗口内通过的请求>=5**，两个条件同时满足后触发降级。窗口期过后关闭断路器，RT最大4900（更大的需要通过-Dcsp.sentinel.statistic.max.rt=XXXX才能生效）。

   - 代码

   ```java
   @GetMapping("/testD")
   public String testD()
   {
       //暂停几秒钟线程
       try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }
       log.info("testD 测试RT");
       return "------testD";
   }
   ```

   - 配置

   ![image-20220824212726686](http://image.xiaobailx.top/images/202208242127733.png)

   - jmeter压测

   ![image-20220824212803736](http://image.xiaobailx.top/images/202208242128780.png)

   - 结论

     永远一秒钟打进来10个线程（大于5个了）调用testD，我们希望200毫秒处理完本次任务，如果超过200毫秒还没处理完，在未来1秒钟的时间窗口内，断路器打开(保险丝跳闸)微服务不可用，保险丝跳闸断电了。后续我停止jmeter，没有这么大的访问量了，断路器关闭(保险丝恢复)，微服务恢复OK。

2. 异常比列（秒级）：QPS >= 5 且异常比例（秒级统计）超过阈值时，触发降级；时间窗口结束后，关闭降级。

   - 代码

   ```java
   @GetMapping("/testD")
   public String testD()
   {
       log.info("testD 测试RT");
       int age = 10/0;
       return "------testD";
   }
   ```

   - 配置

   ![image-20220824213006765](http://image.xiaobailx.top/images/202208242130812.png)

   - jmeter压测

   ![image-20220824213040944](http://image.xiaobailx.top/images/202208242130986.png)

   - 结论

     开启jmeter后，直接高并发发送请求，多次调用达到我们的配置条件了。断路器开启(保险丝跳闸)，微服务不可用了，不再报错error而是服务降级了。

3. 异常数（分钟级）：异常数（分钟统计）超过阈值时，触发降级；时间窗口结束后，关闭降级。

   - 代码

   ```java
   @GetMapping("/testE")
   public String testE()
   {
       log.info("testE 测试异常比例");
       int age = 10/0;
       return "------testE 测试异常比例";
   }
   ```

   - 配置

   ![image-20220824214033195](http://image.xiaobailx.top/images/202208242140242.png)

   http://localhost:8401/testE，第一次访问绝对报错，因为除数不能为零，我们看到error窗口，但是达到5次报错后，进入熔断后降级。
   
   - jmeter压测
   
   ![image-20220824214151002](http://image.xiaobailx.top/images/202208242141049.png)

## 热点key限流

官网：https://github.com/alibaba/Sentinel/wiki/%E7%83%AD%E7%82%B9%E5%8F%82%E6%95%B0%E9%99%90%E6%B5%81

热点即经常访问的数据，很多时候我们希望统计或者限制某个热点数据中访问频次最高的TopN数据，并对其访问进行限流或者其它操作。

**@SentinelResource**

```java
@GetMapping("/testHotKey")
@SentinelResource(value = "testHotKey",blockHandler = "dealHandler_testHotKey")
public String testHotKey(@RequestParam(value = "p1",required = false) String p1, 
                         @RequestParam(value = "p2",required = false) String p2){
    return "------testHotKey";
}
public String dealHandler_testHotKey(String p1,String p2,BlockException exception)
{
    return "-----dealHandler_testHotKey";
}
```

![image-20220824214631462](http://image.xiaobailx.top/images/202208242146520.png)

限流模式只支持QPS模式，固定写死了（这才叫热点）。@SentinelResource注解的方法参数索引，0代表第一个参数，1代表第二个参数，以此类推单机阀值以及统计窗口时长表示在此窗口时间超过阀值就限流。上面的抓图就是第一个参数有值的话，1秒的QPS为1，超过就限流，限流后调用dealHandler_testHotKey支持方法。

测试：

- http://localhost:8401/testHotKey?p1=abc，快速访问返回异常
- http://localhost:8401/testHotKey?p1=abc&p2=33，快速访问返回异常
- http://localhost:8401/testHotKey?p2=abc，快速访问成功返回

**参数例外项**

我们期望p1参数当它是某个特殊值时，它的限流值和平时不一样。假如当p1的值等于5时，它的阈值可以达到200

![image-20220824215205219](http://image.xiaobailx.top/images/202208242152274.png)

- 访问：http://localhost:8401/testHotKey?p1=5，快速访问成功
- 访问：http://localhost:8401/testHotKey?p1=3，快速访问返回异常

>  注意：热点参数的注意点，参数必须是基本类型或者String

## 系统规则

官网：https://github.com/alibaba/Sentinel/wiki/%E7%B3%BB%E7%BB%9F%E8%87%AA%E9%80%82%E5%BA%94%E9%99%90%E6%B5%81

系统保护规则是从应用级别的入口流量进行控制，从单台机器的Iod、CPU使用率、平均RT、入口QPS和并发线程数等几个维度监控应用指标，让系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性。

系统保护规则是应用整体维度的，而不是资源维度的，并且仅对入口流量生效。入口流量指的是进入应用的流量(EntryType.IW),比如Web服务或Dubbo服务端接收的请求，都属于入口流量。

系统规则支持以下的模式：

- Load自适应(仅对Linux/Unix-ike机器生效)：系统的load1作为启发指标，进行自适应系统保护。当系统Ioād1超过设定的启发值，且系统当前的并发线程数超过估算的系统容量时才会触发系统保护(BBR阶段)。系统容量由系统的maxQps*mit估算得出。设定参考值一般是CPU cores 2.5。
- CPU usage(1.5.0+版本)：当系统CPU使用率超过阈值即触发系统保护（取值范围0.0-1.0)，比较灵敏。
- 平均T:当单台机器上所有入口流量的平均T达到阔值即触发系统保护，单位是毫秒。
- 并发线程数：当单台机器上所有入口流量的并发线程数达到阔值即触发系统保护。
- 入口QPS:当单台机器上所有入口流量的QPS达到阈值即触发系统保护。



## @SentinelResource

### 按资源名限流+后续处理

启动 Nacos，执行：`startup.cmd -m standalone`，访问：http://localhost:8848/nacos/#/login 测试

启动 Sentinel，执行：`java -jar sentinel-dashboard-1.7.0.jar`

修改 cloudablibaba-sentinel-service8401

1. 改 POM，添加以依赖

```xml
<dependencies>
    <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <groupId>com.xiaobai.springcloud</groupId>
        <artifactId>cloud-api-commons</artifactId>
        <version>${project.version}</version>
    </dependency>
</dependencies>
```

2. controller，新建 RateLimitController

```java
@RestController
public class RateLimitController
{
    @GetMapping("/byResource")
    @SentinelResource(value = "byResource",blockHandler = "handleException")
    public CommonResult byResource()
    {
        return new CommonResult(200,"按资源名称限流测试OK",new Payment(2020L,"serial001"));
    }
    public CommonResult handleException(BlockException exception)
    {
        return new CommonResult(444,exception.getClass().getCanonicalName()+"\t 服务不可用");
    }
}
```

3. 流控规则配置
   - 配置步骤

	![image-20220827225436433](http://image.xiaobailx.top/images/202208272254855.png)
   
    - 图形配置与代码关系
   
	![image-20220827225502196](http://image.xiaobailx.top/images/202208272255277.png)

6. 测试，启动 8401，访问：http://localhost:8401/byResource，间隔时间大于等于 1s 访问成功，间隔时间小于 1s 返回自定义的限流处理信息。

**遗留问题：** 当 8401 服务关闭，Sentinel 流量控制规则消失。



### 按Url地址限流+后续处理

1. 修改 controller，添加如下方法

```java
@GetMapping("/rateLimit/byUrl")
@SentinelResource(value = "byUrl")
public CommonResult byUrl()
{
    return new CommonResult(200,"按url限流测试OK",new Payment(2020L,"serial002"));
}
```

2. 初步测试，访问：http://localhost:8401/rateLimit/byUrl，访问成功
3. 配置流控规则

![image-20220827225918925](http://image.xiaobailx.top/images/202208272259000.png)

4. 二次测试，连续访问：http://localhost:8401/rateLimit/byUrl，返回 Sentinel 自带的限流处理结果

上述兜底方案面临的问题：

1. 系统默认的，没有体现我们自己的业务要求。
2. 依照现有条件，我们自定义的处理方法又和业务代码耦合在一块，不直观。
3. 每个业务方法都添加一个兜底的，那代码膨胀加剧。
4. 全局统一的处理方法没有体现。



### 客户自定义限流处理逻辑

1. 创建 CustomerBlockHandler 类用于自定义限流处理逻辑

```java
public class CustomerBlockHandler
{
    public static CommonResult handleException(BlockException exception){
        return new CommonResult(2020,"自定义的限流处理信息......CustomerBlockHandler-----1");
    }
    
    public static CommonResult handleException2(BlockException exception){
        return new CommonResult(2020,"自定义的限流处理信息......CustomerBlockHandler------2");
    }
}
```

2.  RateLimitController 添加自定义限流处理逻辑

```java
   /**
     * 自定义通用的限流处理逻辑，
     * blockHandlerClass = CustomerBlockHandler.class
     * blockHandler = handleException2
     * 上述配置：找CustomerBlockHandler类里的handleException2方法进行兜底处理
     */
    /**
     * 自定义通用的限流处理逻辑
     */
    @GetMapping("/rateLimit/customerBlockHandler")
    @SentinelResource(value = "customerBlockHandler",
            blockHandlerClass = CustomerBlockHandler.class, blockHandler = "handleException2")
    public CommonResult customerBlockHandler()
    {
        return new CommonResult(200,"按客户自定义限流处理逻辑");
    }
```

3. 启动微服务后调用：http://localhost:8401/rateLimit/customerBlockHandler
4. Sentinel 控制台添加配置

![image-20220827230635488](http://image.xiaobailx.top/images/202208272306563.png)

5. 再次调用：http://localhost:8401/rateLimit/customerBlockHandler



## 服务熔断

### Ribbon系列

#### 服务提供者9003/9004

1. 新建cloudalibaba-provider-payment9003/9004
2. 改 POM

```xml
<dependencies>
    <!--SpringCloud ailibaba nacos -->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
    <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <groupId>com.xiaobai.springcloud</groupId>
        <artifactId>cloud-api-commons</artifactId>
        <version>${project.version}</version>
    </dependency>
    <!-- SpringBoot整合Web组件 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <!--日常通用jar包配置-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

3. 写 YML（记得该端口号）

```yaml
server:
  port: 9003

spring:
  application:
    name: nacos-payment-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos地址

management:
  endpoints:
    web:
      exposure:
        include: '*'
```

4. 主启动

```java
@SpringBootApplication
@EnableDiscoveryClient
public class PaymentMain9003
{
    public static void main(String[] args) {
            SpringApplication.run(PaymentMain9003.class, args);
    }
}
```

5. 业务类

```java
@RestController
public class PaymentController
{
    @Value("${server.port}")
    private String serverPort;

    public static HashMap<Long,Payment> hashMap = new HashMap<>();
    static
    {
        hashMap.put(1L,new Payment(1L,"28a8c1e3bc2742d8848569891fb42181"));
        hashMap.put(2L,new Payment(2L,"bba8c1e3bc2742d8848569891ac32182"));
        hashMap.put(3L,new Payment(3L,"6ua8c1e3bc2742d8848569891xt92183"));
    }

    @GetMapping(value = "/paymentSQL/{id}")
    public CommonResult<Payment> paymentSQL(@PathVariable("id") Long id)
    {
        Payment payment = hashMap.get(id);
        CommonResult<Payment> result = new CommonResult(200,"from mysql,serverPort:  "+serverPort,payment);
        return result;
    }
}
```

6. 测试，访问：http://localhost:9003/paymentSQL/1



#### 服务消费者84

1. 新建 cloudalibaba-consumer-nacos-order84
2. 改 POM

```xml
<dependencies>
    <!--SpringCloud ailibaba nacos -->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
    <!--SpringCloud ailibaba sentinel -->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
    </dependency>
    <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
    <dependency>
        <groupId>com.xiaobai.springcloud</groupId>
        <artifactId>cloud-api-commons</artifactId>
        <version>${project.version}</version>
    </dependency>
    <!-- SpringBoot整合Web组件 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <!--日常通用jar包配置-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

3. 写 YML

```yaml
server:
  port: 84

spring:
  application:
    name: nacos-order-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    sentinel:
      transport:
        #配置Sentinel dashboard地址
        dashboard: localhost:8080
        #默认8719端口，假如被占用会自动从8719开始依次+1扫描,直至找到未被占用的端口
        port: 8719

#消费者将要去访问的微服务名称(注册成功进nacos的微服务提供者)
service-url:
  nacos-user-service: http://nacos-payment-provider
```

4. 主启动

```java
@EnableDiscoveryClient
@SpringBootApplication
public class OrderNacosMain84
{
    public static void main(String[] args) {
            SpringApplication.run(OrderNacosMain84.class, args);
    }
}
```

5. 配置类

```java
@Configuration
public class ApplicationContextConfig
{
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate()
    {
        return new RestTemplate();
    }
}
```

6. 业务类

```java
@RestController
@Slf4j
public class CircleBreakerController
{
    public static final String SERVICE_URL = "http://nacos-payment-provider";

    @Resource
    private RestTemplate restTemplate;

    @RequestMapping("/consumer/fallback/{id}")
    @SentinelResource(value = "fallback") 
     public CommonResult<Payment> fallback(@PathVariable Long id)
    {
        CommonResult<Payment> result = restTemplate.getForObject(SERVICE_URL + "/paymentSQL/"+id,CommonResult.class,id);

        if (id == 4) {
            throw new IllegalArgumentException ("IllegalArgumentException,非法参数异常....");
        }else if (result.getData() == null) {
            throw new NullPointerException ("NullPointerException,该ID没有对应记录,空指针异常");
        }

        return result;
    }
}
```

7. 测试：http://localhost:84/consumer/fallback/4，直接给客户展示 error 页面，不友好



**添加 fallback 配置**

1. 修改 CircleBreakerController，添加 fallback 配置

```java
@RequestMapping("/consumer/fallback/{id}")
@SentinelResource(value = "fallback",fallback = "handlerFallback") //fallback负责业务异常
public CommonResult<Payment> fallback(@PathVariable Long id)
{
    CommonResult<Payment> result = restTemplate.getForObject(SERVICE_URL + "/paymentSQL/"+id,CommonResult.class,id);

    if (id == 4) {
        throw new IllegalArgumentException ("IllegalArgumentException,非法参数异常....");
    }else if (result.getData() == null) {
        throw new NullPointerException ("NullPointerException,该ID没有对应记录,空指针异常");
    }

    return result;
}

public CommonResult handlerFallback(@PathVariable  Long id,Throwable e) {
    Payment payment = new Payment(id,"null");
    return new CommonResult<>(444,"兜底异常handlerFallback,exception内容  "+e.getMessage(),payment);
}
```

![image-20220827232336442](http://image.xiaobailx.top/images/202208272323514.png)

2. 测试

![image-20220827233104659](http://image.xiaobailx.top/images/202208272331718.png)

![image-20220827233122275](http://image.xiaobailx.top/images/202208272331323.png)

![image-20220827233128339](http://image.xiaobailx.top/images/202208272331386.png)



**添加 blockHandler**

1. 修改 CircleBreakerController，添加 fallback 配置

```java
@RequestMapping("/consumer/fallback/{id}")
@SentinelResource(value = "fallback",blockHandler = "blockHandler") //blockHandler负责在sentinel里面配置的降级限流
public CommonResult<Payment> fallback(@PathVariable Long id)
{
    CommonResult<Payment> result = restTemplate.getForObject(SERVICE_URL + "/paymentSQL/"+id,CommonResult.class,id);
    if (id == 4) {
        throw new IllegalArgumentException ("非法参数异常....");
    }else if (result.getData() == null) {
        throw new NullPointerException ("NullPointerException,该ID没有对应记录");
    }
    return result;
}

public CommonResult blockHandler(@PathVariable  Long id,BlockException blockException) {
    Payment payment = new Payment(id,"null");
    return new CommonResult<>(445,"blockHandler-sentinel限流,无此流水: blockException  "+blockException.getMessage(),payment);
}
```

![image-20220827232830049](http://image.xiaobailx.top/images/202208272328123.png)

2. Sentinel 配置

![image-20220827233622337](http://image.xiaobailx.top/images/202208272336399.png)

3. 测试

![image-20220827232900199](http://image.xiaobailx.top/images/202208272329246.png)



**添加 fallback 和 blockHandler 配置**

1. 修改 CircleBreakerController，添加 fallback 和 blockHandler 配置

```java
@RequestMapping("/consumer/fallback/{id}")
@SentinelResource(value = "fallback",fallback = "handlerFallback",blockHandler = "blockHandler")
public CommonResult<Payment> fallback(@PathVariable Long id)
{
    CommonResult<Payment> result = restTemplate.getForObject(SERVICE_URL + "/paymentSQL/"+id,CommonResult.class,id);
    if (id == 4) {
        throw new IllegalArgumentException ("非法参数异常....");
    }else if (result.getData() == null) {
        throw new NullPointerException ("NullPointerException,该ID没有对应记录");
    }
    return result;
}

public CommonResult handlerFallback(@PathVariable  Long id,Throwable e) {
    Payment payment = new Payment(id,"null");
    return new CommonResult<>(444,"fallback,无此流水,exception  "+e.getMessage(),payment);
}

public CommonResult blockHandler(@PathVariable  Long id,BlockException blockException) {
    Payment payment = new Payment(id,"null");
    return new CommonResult<>(445,"blockHandler-sentinel限流,无此流水: blockException  "+blockException.getMessage(),payment);
}
```

![image-20220827233338591](http://image.xiaobailx.top/images/202208272333673.png)

2. Sentinel 配置

![image-20220827233447671](http://image.xiaobailx.top/images/202208272334729.png)

3. 测试

![image-20220827233510656](http://image.xiaobailx.top/images/202208272335701.png)

若 blockHandler 和 fallback 都进行了配置，则被限流降级而抛出 BlockException 时只会进入 blockHandler 处理逻辑。

**忽略属性...**

1. 修改 controller

```java
@RequestMapping("/consumer/fallback/{id}")
    @SentinelResource(value = "fallback", fallback = "handlerFallback", blockHandler = "blockHandler",
            exceptionsToIgnore = {IllegalArgumentException.class})
    public CommonResult<Payment> fallback(@PathVariable Long id)
    {
        CommonResult<Payment> result = restTemplate.getForObject(SERVICE_URL + "/paymentSQL/"+id,CommonResult.class,id);
        if (id == 4) {
            throw new IllegalArgumentException ("非法参数异常....");
        }else if (result.getData() == null) {
            throw new NullPointerException ("NullPointerException,该ID没有对应记录");
        }
        return result;
    }
```

![image-20220827235610418](http://image.xiaobailx.top/images/202208272356504.png)



### Feign系列

修改84模块

1. 修改 POM，添加依赖

```xml
<!--SpringCloud openfeign -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

2. 修改 YML，添加配置

```yaml
# 激活Sentinel对Feign的支持
feign:
  sentinel:
    enabled: true 
```

3. 业务类
   - 带 @FeignClient 注解的业务接口

    ```java
    /** 使用 fallback 方式是无法获取异常信息的，
     *  如果想要获取异常信息，可以使用 fallbackFactory参数
     */
    @FeignClient(value = "nacos-payment-provider",fallback = PaymentFallbackService.class)//调用中关闭9003服务提供者
    public interface PaymentService
    {
        @GetMapping(value = "/paymentSQL/{id}")
        public CommonResult<Payment> paymentSQL(@PathVariable("id") Long id);
    }
    ```

     - 添加 fallback 类

   ```java
   @Component
   public class PaymentFallbackService implements PaymentService
   {
       @Override
       public CommonResult<Payment> paymentSQL(Long id)
       {
           return new CommonResult<>(444,"服务降级返回,没有该流水信息",new Payment(id, "errorSerial......"));
       }
   }
   ```

4. controller

```java
	//==================OpenFeign
    @Resource
    private PaymentService paymentService;

    @GetMapping(value = "/consumer/paymentSQL/{id}")
    public CommonResult<Payment> paymentSQL(@PathVariable("id") Long id)
    {
        if(id == 4)
        {
            throw new RuntimeException("没有该id");
        }
        return paymentService.paymentSQL(id);
    }
```

5. 主启动，添加 @EnableFeignClients 启动 Feign 的功能
6. 测试，访问：http://localhost:84/consumer/paymentSQL/1，故意关闭9003微服务提供者，看84消费侧自动降级，不会被耗死



### 熔断框架比较

|                | Sentinel                                       | Hystrix               | resilience4j                     |
| -------------- | ---------------------------------------------- | --------------------- | -------------------------------- |
| 隔离策略       | 信号量隔离（并发线程数限质）                   | 线程池隔离/信号量隔离 | 信号量隔离                       |
| 熔断策略       | 基于响应时间、异常比率、异常数                 | 基于异常比率          | 基于异常比率、响应时间           |
| 实时统计实现   | 滑动窗口                                       | 滑动窗口              | Ring Bit Buffer                  |
| 动态规则配置   | 支持多种数据源                                 | 支持多种数据源        | 有限支持                         |
| 扩展性         | 多个扩展点                                     | 插件形式              | 接口形式                         |
| 基于注解支持   | 支持                                           | 支持                  | 支持                             |
| 限流           | 基于QPS,支持基于调用关系的限流                 | 有限支持              | Rate Limiter                     |
| 流量整形       | 支持预热模式、匀速器模式、预热排队模式         | 不支持                | 简单的 Rate Limiter              |
| 系统自适应保护 | 支持                                           | 不支持                | 不支持                           |
| 控制台         | 提供开箱即用的控制台，可配置规则、查看秒级监控 | 简单的监控查看        | 不提供控制台，可对接其他监控系统 |



## 规则持久化

1. 修改 cloudalibaba-sentinel-service8401
2. 修改 POM，添加依赖

```xml
<!--SpringCloud ailibaba sentinel-datasource-nacos -->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
</dependency>
```

3. 修改 YML，添加 Nacos 数据源配置

```yaml
sentinel:
      datasource:
        ds1:
          nacos:
            server-addr: localhost:8848
            dataId: cloudalibaba-sentinel-service
            groupId: DEFAULT_GROUP
            data-type: json
            rule-type: flow
```

4. 添加 Nacos 业务规则配置

![image-20220828002717212](http://image.xiaobailx.top/images/202208280027283.png)

```json
[
    {
        "resource": "/rateLimit/byUrl",
        "limitApp": "default",
        "grade": 1,
        "count": 1,
        "strategy": 0,
        "controlBehavior": 0,
        "clusterMode": false
    }
]
```

- resource：资源名称；
- limitApp：来源应用；
- grade：阈值类型，0表示线程数，1表示QPS；
- count：单机阈值；
- strategy：流控模式，0表示直接，1表示关联，2表示链路；
- controlBehavior：流控效果，0表示快速失败，1表示Warm Up，2表示排队等待；
- clusterMode：是否集群。

5. 启动8401后刷新 sentinel 发现业务规则有了

![image-20220828002810458](http://image.xiaobailx.top/images/202208280028527.png)

6. 快速访问测试接口：http://localhost:8401/rateLimit/byUrl，返回限流信息
7. 停止 8401 再看 sentinel

![image-20220828003009082](http://image.xiaobailx.top/images/202208280030141.png)

8. 重新启动 8401 再看 sentinel，调用：http://localhost:8401/rateLimit/byUrl，配置重新出现了，持久化验证通过

# Seata处理分布式事务

## 分布式事务问题

单体应用被拆分成微服务应用，原来的三个模块被拆分成 **三个独立的应用**，分别使用 **三个独立的数据源**，业务操作需要调用三个服务来完成。此时 **每个服务内部的数据一致性由本地事务来保证，但是全局的数据一致性问题没法保证。**

![image-20220828222510643](http://image.xiaobailx.top/images/202208282225717.png)

总结：一次业务操作需要跨多个数据源或需要跨多个系统进行远程调用，就会产生分布式事务问题。



## Seata简介

官网：http://seata.io/zh-cn/

下载：https://github.com/seata/seata/releases

Seata是一款开源的分布式事务解决方案，致力于在微服务架构下提供高性能和简单易用的分布式事务服务。

一个典型的分布式事务过程由一个事务ID + 三个组件组成。三个组件包括：

1. Transaction Coordinator (TC)：事务协调器，维护全局事务的运行状态，负责协调并驱动全局事务的提交或回滚；
2. Transaction Manager (TM)：控制全局事务的边界，负责开启一个全局事务，并最终发起全局提交或全局回滚的决议；
3. Resource Manager (RM)：控制分支事务，负责分支注册、状态汇报，并接收事务协调器的指令，驱动分支（本地）事务的提交和回滚。

![image-20220828223010974](http://image.xiaobailx.top/images/202208282230048.png)

处理过程：

1. TM 向 TC 申请开启一个全局事务，全局事务创建成功并生成一个全局唯一的 XID；
2. XID 在微服务调用链路的上下文中传播；
3. RM 向 TC 注册分支事务，将其纳入 XID 对应全局事务的管辖；
4. TM 向 TC 发起针对 XID 的全局提交或回滚决议；
5. TC 调度 XID 下管辖的全部分支事务完成提交或回滚请求。

![image-20220828223216587](http://image.xiaobailx.top/images/202208282232659.png)

Seata 只需两个注解：本地@Transactional 和 全局@GlobalTransactional 即可实现分布式事务控制。



## Seata-Server安装

1. 下载：https://github.com/seata/seata/releases，课程版本下载的是seata-server-0.9.0.zip

2. seata-server-0.9.0.zip 解压到指定目录并修改 conf 目录下的 file.conf 配置文件

   - 备份 file.conf 文件

   - 修改自定义事务组名称+事务日志存储模式为db+数据库连接信息

     service 模块

     ```shell
     service {
       vgroup_mapping.my_test_tx_group = "fsp_tx_group"
     }
     ```

     store 模块

     ```shell
     ## transaction log store
     store {
       ## store mode: file、db
       mode = "db"
      
       ## database store
       db {
         driver-class-name = "com.mysql.jdbc.Driver"
         url = "jdbc:mysql://127.0.0.1:3306/seata"
         user = "root"
         password = "root"
       }
     }
     ```

3. mysql5.7 数据库新建库 seata，运行在 seata 安装目录中 conf 目录下的 db_store.sql

```sql
create database seata;
use seata;
source D:\Devware\seata-server-0.9.0\conf\db_store.sql
```

4. 修改 seata 安装目录下 conf 目录下的 registry.conf 配置文件

```shell
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  type = "nacos" #指明注册中心为nacos
 
  nacos {
    serverAddr = "localhost:8848" #修改nacos连接信息
  }
```

5. 启动 Nacos 端口号8848，执行 `startup.cmd -m standalone`
6. 启动 seata-server，运行 seata 安装目录中 bin 目录下的 `seata-server.bat`



## 订单/库存/账户业务数据库准备

创建三个服务，一个订单服务，一个库存服务，一个账户服务。当用户下单时，会在订单服务中创建一个订单，然后通过远程调用库存服务来扣减下单商品的库存，再通过远程调用账户服务来扣减用户账户里面的余额，最后在订单服务中修改订单状态为已完成。该操作跨越三个数据库，有两次远程调用，很明显会有分布式事务问题。

1. 建库

```sql
CREATE DATABASE seata_order;
CREATE DATABASE seata_storage; 
CREATE DATABASE seata_account;
```

2. 按照上述 3 库分别建对应业务表

```sql
#seata_order库下建t_order表
CREATE TABLE t_order (
  `id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
  `user_id` BIGINT(11) DEFAULT NULL COMMENT '用户id',
  `product_id` BIGINT(11) DEFAULT NULL COMMENT '产品id',
  `count` INT(11) DEFAULT NULL COMMENT '数量',
  `money` DECIMAL(11,0) DEFAULT NULL COMMENT '金额',
  `status` INT(1) DEFAULT NULL COMMENT '订单状态：0：创建中；1：已完结' 
) ENGINE=INNODB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8;
 
SELECT * FROM t_order;

#seata_storage库下建t_storage 表
CREATE TABLE t_storage (
 `id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
 `product_id` BIGINT(11) DEFAULT NULL COMMENT '产品id',
 `total` INT(11) DEFAULT NULL COMMENT '总库存',
 `used` INT(11) DEFAULT NULL COMMENT '已用库存',
 `residue` INT(11) DEFAULT NULL COMMENT '剩余库存'
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
 
 
INSERT INTO seata_storage.t_storage(`id`, `product_id`, `total`, `used`, `residue`)
VALUES ('1', '1', '100', '0', '100');
 
SELECT * FROM t_storage;

#seata_account库下建t_account 表
CREATE TABLE t_account (
  `id` BIGINT(11) NOT NULL AUTO_INCREMENT PRIMARY KEY COMMENT 'id',
  `user_id` BIGINT(11) DEFAULT NULL COMMENT '用户id',
  `total` DECIMAL(10,0) DEFAULT NULL COMMENT '总额度',
  `used` DECIMAL(10,0) DEFAULT NULL COMMENT '已用余额',
  `residue` DECIMAL(10,0) DEFAULT '0' COMMENT '剩余可用额度'
) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
 
INSERT INTO seata_account.t_account(`id`, `user_id`, `total`, `used`, `residue`)  VALUES ('1', '1', '1000', '0', '1000');
 
SELECT * FROM t_account;
```

3. 按照上述3库分别建对应的回滚日志表，导入 seata 安装目录 conf 下的 _undo_log.sql 文件

```sql
source D:\Devware\seata-server-0.9.0\conf\db_undo_log.sql
```



## 订单/库存/账户业务微服务准备

### seata-order-service2001

1. 新建 moudle，seata-order-service2001
2. 修改 POM

```xml
<dependencies>
    <!--nacos-->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
    <!--seata-->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
        <exclusions>
            <exclusion>
                <artifactId>seata-all</artifactId>
                <groupId>io.seata</groupId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>io.seata</groupId>
        <artifactId>seata-all</artifactId>
        <version>0.9.0</version>
    </dependency>
    <!--feign-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-openfeign</artifactId>
    </dependency>
    <!--web-actuator-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <!--mysql-druid-->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.37</version>
    </dependency>
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid-spring-boot-starter</artifactId>
        <version>1.1.10</version>
    </dependency>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>2.0.0</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>
```

3. 写 YML

```yaml
server:
  port: 2001

spring:
  application:
    name: seata-order-service
  cloud:
    alibaba:
      seata:
        #自定义事务组名称需要与seata-server中的对应
        tx-service-group: fsp_tx_group
    nacos:
      discovery:
        server-addr: localhost:8848
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/seata_order
    username: root
    password: 123456

feign:
  hystrix:
    enabled: false

logging:
  level:
    io:
      seata: info

mybatis:
  mapperLocations: classpath:mapper/*.xml
```

4.  配置文件 

   - file.conf

   ```shell
   transport {
     # tcp udt unix-domain-socket
     type = "TCP"
     #NIO NATIVE
     server = "NIO"
     #enable heartbeat
     heartbeat = true
     #thread factory for netty
     thread-factory {
       boss-thread-prefix = "NettyBoss"
       worker-thread-prefix = "NettyServerNIOWorker"
       server-executor-thread-prefix = "NettyServerBizHandler"
       share-boss-worker = false
       client-selector-thread-prefix = "NettyClientSelector"
       client-selector-thread-size = 1
       client-worker-thread-prefix = "NettyClientWorkerThread"
       # netty boss thread size,will not be used for UDT
       boss-thread-size = 1
       #auto default pin or 8
       worker-thread-size = 8
     }
     shutdown {
       # when destroy server, wait seconds
       wait = 3
     }
     serialization = "seata"
     compressor = "none"
   }
    
   service {
    
     vgroup_mapping.fsp_tx_group = "default" #修改自定义事务组名称
    
     default.grouplist = "127.0.0.1:8091"
     enableDegrade = false
     disable = false
     max.commit.retry.timeout = "-1"
     max.rollback.retry.timeout = "-1"
     disableGlobalTransaction = false
   }
    
    
   client {
     async.commit.buffer.limit = 10000
     lock {
       retry.internal = 10
       retry.times = 30
     }
     report.retry.count = 5
     tm.commit.retry.count = 1
     tm.rollback.retry.count = 1
   }
    
   ## transaction log store
   store {
     ## store mode: file、db
     mode = "db"
    
     ## file store
     file {
       dir = "sessionStore"
    
       # branch session size , if exceeded first try compress lockkey, still exceeded throws exceptions
       max-branch-session-size = 16384
       # globe session size , if exceeded throws exceptions
       max-global-session-size = 512
       # file buffer size , if exceeded allocate new buffer
       file-write-buffer-cache-size = 16384
       # when recover batch read size
       session.reload.read_size = 100
       # async, sync
       flush-disk-mode = async
     }
    
     ## database store
     db {
       ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp) etc.
       datasource = "dbcp"
       ## mysql/oracle/h2/oceanbase etc.
       db-type = "mysql"
       driver-class-name = "com.mysql.jdbc.Driver"
       url = "jdbc:mysql://127.0.0.1:3306/seata"
       user = "root"
       password = "123456"
       min-conn = 1
       max-conn = 3
       global.table = "global_table"
       branch.table = "branch_table"
       lock-table = "lock_table"
       query-limit = 100
     }
   }
   lock {
     ## the lock store mode: local、remote
     mode = "remote"
    
     local {
       ## store locks in user's database
     }
    
     remote {
       ## store locks in the seata's server
     }
   }
   recovery {
     #schedule committing retry period in milliseconds
     committing-retry-period = 1000
     #schedule asyn committing retry period in milliseconds
     asyn-committing-retry-period = 1000
     #schedule rollbacking retry period in milliseconds
     rollbacking-retry-period = 1000
     #schedule timeout retry period in milliseconds
     timeout-retry-period = 1000
   }
    
   transaction {
     undo.data.validation = true
     undo.log.serialization = "jackson"
     undo.log.save.days = 7
     #schedule delete expired undo_log in milliseconds
     undo.log.delete.period = 86400000
     undo.log.table = "undo_log"
   }
    
   ## metrics settings
   metrics {
     enabled = false
     registry-type = "compact"
     # multi exporters use comma divided
     exporter-list = "prometheus"
     exporter-prometheus-port = 9898
   }
    
   support {
     ## spring
     spring {
       # auto proxy the DataSource bean
       datasource.autoproxy = false
     }
   }
   ```

   - registry.conf

   ```shell
   registry {
     # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
     type = "nacos"
    
     nacos {
       serverAddr = "localhost:8848"
       namespace = ""
       cluster = "default"
     }
     eureka {
       serviceUrl = "http://localhost:8761/eureka"
       application = "default"
       weight = "1"
     }
     redis {
       serverAddr = "localhost:6379"
       db = "0"
     }
     zk {
       cluster = "default"
       serverAddr = "127.0.0.1:2181"
       session.timeout = 6000
       connect.timeout = 2000
     }
     consul {
       cluster = "default"
       serverAddr = "127.0.0.1:8500"
     }
     etcd3 {
       cluster = "default"
       serverAddr = "http://localhost:2379"
     }
     sofa {
       serverAddr = "127.0.0.1:9603"
       application = "default"
       region = "DEFAULT_ZONE"
       datacenter = "DefaultDataCenter"
       cluster = "default"
       group = "SEATA_GROUP"
       addressWaitTime = "3000"
     }
     file {
       name = "file.conf"
     }
   }
    
   config {
     # file、nacos 、apollo、zk、consul、etcd3
     type = "file"
    
     nacos {
       serverAddr = "localhost"
       namespace = ""
     }
     consul {
       serverAddr = "127.0.0.1:8500"
     }
     apollo {
       app.id = "seata-server"
       apollo.meta = "http://192.168.1.204:8801"
     }
     zk {
       serverAddr = "127.0.0.1:2181"
       session.timeout = 6000
       connect.timeout = 2000
     }
     etcd3 {
       serverAddr = "http://localhost:2379"
     }
     file {
       name = "file.conf"
     }
   }
   ```

5. domain

   - CommonResult

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class CommonResult<T>
   {
       private Integer code;
       private String  message;
       private T       data;
   
       public CommonResult(Integer code, String message)
       {
           this(code,message,null);
       }
   }
   ```

   - Order

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class Order
   {
       private Long id;
   
       private Long userId;
   
       private Long productId;
   
       private Integer count;
   
       private BigDecimal money;
   
       /**
        * 订单状态：0：创建中；1：已完结
        */
       private Integer status;
   }
   ```

6. Dao 接口实现及实现

   - OrderDao

   ```java
   @Mapper
   public interface OrderDao {
   
       /**
        * 创建订单
        */
       void create(Order order);
   
       /**
        * 修改订单金额
        */
       void update(@Param("userId") Long userId, @Param("status") Integer status);
   }
   ```

   - resource 目录下添加 mapper 文件夹后添加 OrderMapper.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
   
   <mapper namespace="com.xiaobai.springcloud.alibaba.dao.OrderDao">
   
       <resultMap id="BaseResultMap" type="com.xiaobai.springcloud.alibaba.domain.Order">
           <id column="id" property="id" jdbcType="BIGINT"/>
           <result column="user_id" property="userId" jdbcType="BIGINT"/>
           <result column="product_id" property="productId" jdbcType="BIGINT"/>
           <result column="count" property="count" jdbcType="INTEGER"/>
           <result column="money" property="money" jdbcType="DECIMAL"/>
           <result column="status" property="status" jdbcType="INTEGER"/>
       </resultMap>
   
       <insert id="create">
           INSERT INTO `t_order` (`id`, `user_id`, `product_id`, `count`, `money`, `status`)
           VALUES (NULL, #{userId}, #{productId}, #{count}, #{money}, 0);
       </insert>
   
       <update id="update">
           UPDATE `t_order`
           SET status = 1
           WHERE user_id = #{userId} AND status = #{status};
       </update>
   </mapper>
   ```

7. Service接口及实现

   - OrderService

   ```java
   public interface OrderService {
   
       /**
        * 创建订单
        */
       void create(Order order);
   }
   ```

   - OrderServiceImpl

   ```java
   @Service
   @Slf4j
   public class OrderServiceImpl implements OrderService
   {
       @Resource
       private OrderDao orderDao;
   
       @Resource
       private StorageService storageService;
   
       @Resource
       private AccountService accountService;
   
       /**
        * 创建订单->调用库存服务扣减库存->调用账户服务扣减账户余额->修改订单状态
        * 简单说：
        * 下订单->减库存->减余额->改状态
        */
       @Override
       @GlobalTransactional(name = "fsp-create-order",rollbackFor = Exception.class)
       public void create(Order order) {
           log.info("------->下单开始");
           //本应用创建订单
           orderDao.create(order);
   
           //远程调用库存服务扣减库存
           log.info("------->order-service中扣减库存开始");
           storageService.decrease(order.getProductId(),order.getCount());
           log.info("------->order-service中扣减库存结束");
   
           //远程调用账户服务扣减余额
           log.info("------->order-service中扣减余额开始");
           accountService.decrease(order.getUserId(),order.getMoney());
           log.info("------->order-service中扣减余额结束");
   
           //修改订单状态为已完成
           log.info("------->order-service中修改订单状态开始");
           orderDao.update(order.getUserId(),0);
           log.info("------->order-service中修改订单状态结束");
   
           log.info("------->下单结束");
       }
   }
   ```

   - StorageService

   ```java
   @FeignClient(value = "seata-storage-service")
   public interface StorageService {
   
       /**
        * 扣减库存
        */
       @PostMapping(value = "/storage/decrease")
       CommonResult decrease(@RequestParam("productId") Long productId, @RequestParam("count") Integer count);
   }
   ```

   - AccountService

   ```java
   @FeignClient(value = "seata-account-service")
   public interface AccountService {
   
       /**
        * 扣减账户余额
        */
       //@RequestMapping(value = "/account/decrease", method = RequestMethod.POST, produces = "application/json; charset=UTF-8")
       @PostMapping("/account/decrease")
       CommonResult decrease(@RequestParam("userId") Long userId, @RequestParam("money") BigDecimal money);
   }
   ```

8. controller

```java
@RestController
public class OrderController {

    @Autowired
    private OrderService orderService;

    /**
     * 创建订单
     */
    @GetMapping("/order/create")
    public CommonResult create( Order order) {
        orderService.create(order);
        return new CommonResult(200, "订单创建成功!");
    }
}
```

10. Config配置

    - MyBatisConfig

    ```java
    @Configuration
    @MapperScan({"com.xiaobai.springcloud.alibaba.dao"})
        public class MyBatisConfig {
        }
    }
    ```

    - DataSourceProxyConfig

    ```java
    @Configuration
    public class DataSourceProxyConfig {
    
        @Value("${mybatis.mapperLocations}")
        private String mapperLocations;
    
        @Bean
        @ConfigurationProperties(prefix = "spring.datasource")
        public DataSource druidDataSource(){
            return new DruidDataSource();
        }
    
        @Bean
        public DataSourceProxy dataSourceProxy(DataSource dataSource) {
            return new DataSourceProxy(dataSource);
        }
    
        @Bean
        public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception {
            SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
            sqlSessionFactoryBean.setDataSource(dataSourceProxy);
            sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources(mapperLocations));
            sqlSessionFactoryBean.setTransactionFactory(new SpringManagedTransactionFactory());
            return sqlSessionFactoryBean.getObject();
        }
    }
    ```



### seata-storage-service2002

1. 新建moudle，seata-storage-service2002

2. 改 POM

```xml
<dependencies>
        <!--nacos-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!--seata-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
            <exclusions>
                <exclusion>
                    <artifactId>seata-all</artifactId>
                    <groupId>io.seata</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>io.seata</groupId>
            <artifactId>seata-all</artifactId>
            <version>0.9.0</version>
        </dependency>
        <!--feign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.0.0</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.37</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
    </dependencies>
```

3. 写 YML

```yaml
server:
  port: 2002

spring:
  application:
    name: seata-storage-service
  cloud:
    alibaba:
      seata:
        tx-service-group: fsp_tx_group
    nacos:
      discovery:
        server-addr: localhost:8848
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/seata_storage
    username: root
    password: 123456

logging:
  level:
    io:
      seata: info

mybatis:
  mapperLocations: classpath:mapper/*.xml
```

4. 配置文件

   - file.conf

   ```shell
   transport {
     # tcp udt unix-domain-socket
     type = "TCP"
     #NIO NATIVE
     server = "NIO"
     #enable heartbeat
     heartbeat = true
     #thread factory for netty
     thread-factory {
       boss-thread-prefix = "NettyBoss"
       worker-thread-prefix = "NettyServerNIOWorker"
       server-executor-thread-prefix = "NettyServerBizHandler"
       share-boss-worker = false
       client-selector-thread-prefix = "NettyClientSelector"
       client-selector-thread-size = 1
       client-worker-thread-prefix = "NettyClientWorkerThread"
       # netty boss thread size,will not be used for UDT
       boss-thread-size = 1
       #auto default pin or 8
       worker-thread-size = 8
     }
     shutdown {
       # when destroy server, wait seconds
       wait = 3
     }
     serialization = "seata"
     compressor = "none"
   }
   
   service {
     #vgroup->rgroup
     vgroup_mapping.fsp_tx_group = "default"
     #only support single node
     default.grouplist = "127.0.0.1:8091"
     #degrade current not support
     enableDegrade = false
     #disable
     disable = false
     #unit ms,s,m,h,d represents milliseconds, seconds, minutes, hours, days, default permanent
     max.commit.retry.timeout = "-1"
     max.rollback.retry.timeout = "-1"
     disableGlobalTransaction = false
   }
   
   client {
     async.commit.buffer.limit = 10000
     lock {
       retry.internal = 10
       retry.times = 30
     }
     report.retry.count = 5
     tm.commit.retry.count = 1
     tm.rollback.retry.count = 1
   }
   
   transaction {
     undo.data.validation = true
     undo.log.serialization = "jackson"
     undo.log.save.days = 7
     #schedule delete expired undo_log in milliseconds
     undo.log.delete.period = 86400000
     undo.log.table = "undo_log"
   }
   
   support {
     ## spring
     spring {
       # auto proxy the DataSource bean
       datasource.autoproxy = false
     }
   }
   ```

   - registry.conf

   ```shell
   registry {
     # file 、nacos 、eureka、redis、zk
     type = "nacos"
   
     nacos {
       serverAddr = "localhost:8848"
       namespace = ""
       cluster = "default"
     }
     eureka {
       serviceUrl = "http://localhost:8761/eureka"
       application = "default"
       weight = "1"
     }
     redis {
       serverAddr = "localhost:6381"
       db = "0"
     }
     zk {
       cluster = "default"
       serverAddr = "127.0.0.1:2181"
       session.timeout = 6000
       connect.timeout = 2000
     }
     file {
       name = "file.conf"
     }
   }
   
   config {
     # file、nacos 、apollo、zk
     type = "file"
   
     nacos {
       serverAddr = "localhost"
       namespace = ""
       cluster = "default"
     }
     apollo {
       app.id = "fescar-server"
       apollo.meta = "http://192.168.1.204:8801"
     }
     zk {
       serverAddr = "127.0.0.1:2181"
       session.timeout = 6000
       connect.timeout = 2000
     }
     file {
       name = "file.conf"
     }
   }
   ```

5. domain

   - CommanResult

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class CommonResult<T>
   {
       private Integer code;
       private String  message;
       private T       data;
   
       public CommonResult(Integer code, String message)
       {
           this(code,message,null);
       }
   }
   ```

   - Storage

   ```java
   @Data
   public class Storage {
   
       private Long id;
   
       /**
        * 产品id
        */
       private Long productId;
   
       /**
        * 总库存
        */
       private Integer total;
   
       /**
        * 已用库存
        */
       private Integer used;
   
       /**
        * 剩余库存
        */
       private Integer residue;
   }
   ```

6. Dao接口及实现

   - StorageDao

   ```java
   @Mapper
   public interface StorageDao {
   
       /**
        * 扣减库存
        */
       void decrease(@Param("productId") Long productId, @Param("count") Integer count);
   }
   ```

   - resources文件夹下新建mapper文件夹后添加 StorageMapper.xml

   ```xml
   <mapper namespace="com.xiaobai.springcloud.alibaba.dao.StorageDao">
   
       <resultMap id="BaseResultMap" type="com.xiaobai.springcloud.alibaba.domain.Storage">
           <id column="id" property="id" jdbcType="BIGINT"/>
           <result column="product_id" property="productId" jdbcType="BIGINT"/>
           <result column="total" property="total" jdbcType="INTEGER"/>
           <result column="used" property="used" jdbcType="INTEGER"/>
           <result column="residue" property="residue" jdbcType="INTEGER"/>
       </resultMap>
   
       <update id="decrease">
           UPDATE t_storage
           SET used    = used + #{count},
               residue = residue - #{count}
           WHERE product_id = #{productId}
       </update>
   
   </mapper>
   ```

7. Service接口及实现

   - StorageService

   ```java
   public interface StorageService {
       /**
        * 扣减库存
        */
       void decrease(Long productId, Integer count);
   }
   ```

   - StorageServiceImpl 

   ```java
   @Service
   public class StorageServiceImpl implements StorageService {
   
       private static final Logger LOGGER = LoggerFactory.getLogger(StorageServiceImpl.class);
   
       @Resource
       private StorageDao storageDao;
   
       /**
        * 扣减库存
        */
       @Override
       public void decrease(Long productId, Integer count) {
           LOGGER.info("------->storage-service中扣减库存开始");
           storageDao.decrease(productId,count);
           LOGGER.info("------->storage-service中扣减库存结束");
       }
   }
   ```

8. Controller

```java
@RestController
public class StorageController {

    @Autowired
    private StorageService storageService;

    /**
     * 扣减库存
     */
    @RequestMapping("/storage/decrease")
    public CommonResult decrease(Long productId, Integer count) {
        storageService.decrease(productId, count);
        return new CommonResult(200,"扣减库存成功！");
    }
}
```

9. Config配置

   - MyBatisConfig

   ```java
   @Configuration
   @MapperScan({"com.xiaobai.springcloud.alibaba.dao"})
   public class MyBatisConfig {
   }
   ```

   - DataSourceProxyConfig

   ```java
   @Configuration
   public class DataSourceProxyConfig {
   
       @Value("${mybatis.mapperLocations}")
       private String mapperLocations;
   
       @Bean
       @ConfigurationProperties(prefix = "spring.datasource")
       public DataSource druidDataSource(){
           return new DruidDataSource();
       }
   
       @Bean
       public DataSourceProxy dataSourceProxy(DataSource dataSource) {
           return new DataSourceProxy(dataSource);
       }
   
       @Bean
       public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception {
           SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
           sqlSessionFactoryBean.setDataSource(dataSourceProxy);
           sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources(mapperLocations));
           sqlSessionFactoryBean.setTransactionFactory(new SpringManagedTransactionFactory());
           return sqlSessionFactoryBean.getObject();
       }
   
   }
   ```

10. 主启动

```java
@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
@EnableDiscoveryClient
@EnableFeignClients
public class SeataStorageServiceApplication2002 {

    public static void main(String[] args) {
        SpringApplication.run(SeataStorageServiceApplication2002.class, args);
    }

}
```



### seata-account-service2003

1. 新建moudle，seata-account-service2003
2. 改 POM

```xml
<dependencies>
        <!--nacos-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!--seata-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
            <exclusions>
                <exclusion>
                    <artifactId>seata-all</artifactId>
                    <groupId>io.seata</groupId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>io.seata</groupId>
            <artifactId>seata-all</artifactId>
            <version>0.9.0</version>
        </dependency>
        <!--feign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.0.0</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.37</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
    </dependencies>
```

3. 写YML

```yaml
server:
  port: 2003

spring:
  application:
    name: seata-account-service
  cloud:
    alibaba:
      seata:
        tx-service-group: fsp_tx_group
    nacos:
      discovery:
        server-addr: localhost:8848
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/seata_account
    username: root
    password: 123456

feign:
  hystrix:
    enabled: false

logging:
  level:
    io:
      seata: info

mybatis:
  mapperLocations: classpath:mapper/*.xml
```

4. 配置文件

   - file.conf

   ```shell
   transport {
     # tcp udt unix-domain-socket
     type = "TCP"
     #NIO NATIVE
     server = "NIO"
     #enable heartbeat
     heartbeat = true
     #thread factory for netty
     thread-factory {
       boss-thread-prefix = "NettyBoss"
       worker-thread-prefix = "NettyServerNIOWorker"
       server-executor-thread-prefix = "NettyServerBizHandler"
       share-boss-worker = false
       client-selector-thread-prefix = "NettyClientSelector"
       client-selector-thread-size = 1
       client-worker-thread-prefix = "NettyClientWorkerThread"
       # netty boss thread size,will not be used for UDT
       boss-thread-size = 1
       #auto default pin or 8
       worker-thread-size = 8
     }
     shutdown {
       # when destroy server, wait seconds
       wait = 3
     }
     serialization = "seata"
     compressor = "none"
   }
   
   service {
   
     vgroup_mapping.fsp_tx_group = "default" #修改自定义事务组名称
   
     default.grouplist = "127.0.0.1:8091"
     enableDegrade = false
     disable = false
     max.commit.retry.timeout = "-1"
     max.rollback.retry.timeout = "-1"
     disableGlobalTransaction = false
   }
   
   
   client {
     async.commit.buffer.limit = 10000
     lock {
       retry.internal = 10
       retry.times = 30
     }
     report.retry.count = 5
     tm.commit.retry.count = 1
     tm.rollback.retry.count = 1
   }
   
   ## transaction log store
   store {
     ## store mode: file、db
     mode = "db"
   
     ## file store
     file {
       dir = "sessionStore"
   
       # branch session size , if exceeded first try compress lockkey, still exceeded throws exceptions
       max-branch-session-size = 16384
       # globe session size , if exceeded throws exceptions
       max-global-session-size = 512
       # file buffer size , if exceeded allocate new buffer
       file-write-buffer-cache-size = 16384
       # when recover batch read size
       session.reload.read_size = 100
       # async, sync
       flush-disk-mode = async
     }
   
     ## database store
     db {
       ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp) etc.
       datasource = "dbcp"
       ## mysql/oracle/h2/oceanbase etc.
       db-type = "mysql"
       driver-class-name = "com.mysql.jdbc.Driver"
       url = "jdbc:mysql://127.0.0.1:3306/seata"
       user = "root"
       password = "123456"
       min-conn = 1
       max-conn = 3
       global.table = "global_table"
       branch.table = "branch_table"
       lock-table = "lock_table"
       query-limit = 100
     }
   }
   lock {
     ## the lock store mode: local、remote
     mode = "remote"
   
     local {
       ## store locks in user's database
     }
   
     remote {
       ## store locks in the seata's server
     }
   }
   recovery {
     #schedule committing retry period in milliseconds
     committing-retry-period = 1000
     #schedule asyn committing retry period in milliseconds
     asyn-committing-retry-period = 1000
     #schedule rollbacking retry period in milliseconds
     rollbacking-retry-period = 1000
     #schedule timeout retry period in milliseconds
     timeout-retry-period = 1000
   }
   
   transaction {
     undo.data.validation = true
     undo.log.serialization = "jackson"
     undo.log.save.days = 7
     #schedule delete expired undo_log in milliseconds
     undo.log.delete.period = 86400000
     undo.log.table = "undo_log"
   }
   
   ## metrics settings
   metrics {
     enabled = false
     registry-type = "compact"
     # multi exporters use comma divided
     exporter-list = "prometheus"
     exporter-prometheus-port = 9898
   }
   
   support {
     ## spring
     spring {
       # auto proxy the DataSource bean
       datasource.autoproxy = false
     }
   }
   ```

   - registry.conf

   ```java
   registry {
     # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
     type = "nacos"
   
     nacos {
       serverAddr = "localhost:8848"
       namespace = ""
       cluster = "default"
     }
     eureka {
       serviceUrl = "http://localhost:8761/eureka"
       application = "default"
       weight = "1"
     }
     redis {
       serverAddr = "localhost:6379"
       db = "0"
     }
     zk {
       cluster = "default"
       serverAddr = "127.0.0.1:2181"
       session.timeout = 6000
       connect.timeout = 2000
     }
     consul {
       cluster = "default"
       serverAddr = "127.0.0.1:8500"
     }
     etcd3 {
       cluster = "default"
       serverAddr = "http://localhost:2379"
     }
     sofa {
       serverAddr = "127.0.0.1:9603"
       application = "default"
       region = "DEFAULT_ZONE"
       datacenter = "DefaultDataCenter"
       cluster = "default"
       group = "SEATA_GROUP"
       addressWaitTime = "3000"
     }
     file {
       name = "file.conf"
     }
   }
   
   config {
     # file、nacos 、apollo、zk、consul、etcd3
     type = "file"
   
     nacos {
       serverAddr = "localhost"
       namespace = ""
     }
     consul {
       serverAddr = "127.0.0.1:8500"
     }
     apollo {
       app.id = "seata-server"
       apollo.meta = "http://192.168.1.204:8801"
     }
     zk {
       serverAddr = "127.0.0.1:2181"
       session.timeout = 6000
       connect.timeout = 2000
     }
     etcd3 {
       serverAddr = "http://localhost:2379"
     }
     file {
       name = "file.conf"
     }
   }
   ```

5. domain

   - CommonResult

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class CommonResult<T>
   {
       private Integer code;
       private String  message;
       private T       data;
   
       public CommonResult(Integer code, String message)
       {
           this(code,message,null);
       }
   }
   ```

   - Account

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class Account {
   
       private Long id;
   
       /**
        * 用户id
        */
       private Long userId;
   
       /**
        * 总额度
        */
       private BigDecimal total;
   
       /**
        * 已用额度
        */
       private BigDecimal used;
   
       /**
        * 剩余额度
        */
       private BigDecimal residue;
   }
   ```

6. Dao接口及实现

   - AccountDao

   ```java
   @Mapper
   public interface AccountDao {
   
       /**
        * 扣减账户余额
        */
       void decrease(@Param("userId") Long userId, @Param("money") BigDecimal money);
   }
   ```

   - resources文件夹下新建mapper文件夹后添加AccountMapper.xml

   ```java
   <mapper namespace="com.xiaobai.springcloud.alibaba.dao.AccountDao">
   
       <resultMap id="BaseResultMap" type="com.xiaobai.springcloud.alibaba.domain.Account">
           <id column="id" property="id" jdbcType="BIGINT"/>
           <result column="user_id" property="userId" jdbcType="BIGINT"/>
           <result column="total" property="total" jdbcType="DECIMAL"/>
           <result column="used" property="used" jdbcType="DECIMAL"/>
           <result column="residue" property="residue" jdbcType="DECIMAL"/>
       </resultMap>
   
       <update id="decrease">
           UPDATE t_account
           SET
             residue = residue - #{money},used = used + #{money}
           WHERE
             user_id = #{userId};
       </update>
   
   </mapper>
   ```

7. Service接口及实现

   - AccountService

   ```java
   public interface AccountService {
   
       /**
        * 扣减账户余额
        * @param userId 用户id
        * @param money 金额
        */
       void decrease(@RequestParam("userId") Long userId, @RequestParam("money") BigDecimal money);
   }
   ```

   - AccountServiceImpl 

   ```java
   /**
    * 账户业务实现类
    * Created by zzyy on 2019/11/11.
    */
   @Service
   public class AccountServiceImpl implements AccountService {
   
       private static final Logger LOGGER = LoggerFactory.getLogger(AccountServiceImpl.class);
   
       @Resource
       AccountDao accountDao;
   
       /**
        * 扣减账户余额
        */
       @Override
       public void decrease(Long userId, BigDecimal money) {
           LOGGER.info("------->account-service中扣减账户余额开始");
           //模拟超时异常，全局事务回滚
           //暂停几秒钟线程
           //try { TimeUnit.SECONDS.sleep(30); } catch (InterruptedException e) { e.printStackTrace(); }
           accountDao.decrease(userId,money);
           LOGGER.info("------->account-service中扣减账户余额结束");
       }
   }
   ```

8. Controller

```java
@RestController
public class AccountController {

    @Resource
    AccountService accountService;

    /**
     * 扣减账户余额
     */
    @RequestMapping("/account/decrease")
    public CommonResult decrease(@RequestParam("userId") Long userId, @RequestParam("money") BigDecimal money){
        accountService.decrease(userId,money);
        return new CommonResult(200,"扣减账户余额成功！");
    }
}
```

9. Config配置

   - MyBatisConfig

   ```java
   @Configuration
   @MapperScan({"com.xiaobai.springcloud.alibaba.dao"})
   public class MyBatisConfig {
   }
   ```

   - DataSourceProxyConfig

   ```java
   @Configuration
   public class DataSourceProxyConfig {
   
       @Value("${mybatis.mapperLocations}")
       private String mapperLocations;
   
       @Bean
       @ConfigurationProperties(prefix = "spring.datasource")
       public DataSource druidDataSource(){
           return new DruidDataSource();
       }
   
       @Bean
       public DataSourceProxy dataSourceProxy(DataSource dataSource) {
           return new DataSourceProxy(dataSource);
       }
   
       @Bean
       public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception {
           SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
           sqlSessionFactoryBean.setDataSource(dataSourceProxy);
           sqlSessionFactoryBean.setMapperLocations(new PathMatchingResourcePatternResolver().getResources(mapperLocations));
           sqlSessionFactoryBean.setTransactionFactory(new SpringManagedTransactionFactory());
           return sqlSessionFactoryBean.getObject();
       }
   
   }
   ```

10. 主启动

```java
@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
@EnableDiscoveryClient
@EnableFeignClients
public class SeataAccountMainApp2003
{
    public static void main(String[] args)
    {
        SpringApplication.run(SeataAccountMainApp2003.class, args);
    }
}
```



## 订单/库存/账户业务微服务测试

1. 添加订单：http://localhost:2001/order/create?userId=1&productId=1&count=10&money=100
2. 初次测试，t_order、t_storage、t_account 三个 表添加数据均符合预期；
3. 给 AccountServiceImpl 添加超时，再次测试；
4. 再次测试，当库存和账户金额扣减后，订单状态并没有设置为已经完成，没有从零改为1。而且由于feign的重试机制，账户余额还有可能被多次扣减；
5. 使用 @GlobalTransactional 对 OrderServiceImpl 进行全局事务处理；
6. 再次测试，下单后数据库数据并没有任何改变，记录都添加不进来。



## 补充内容

**再看TC/TM/RM三大组件**

![image-20220829221013662](http://image.xiaobailx.top/images/202208292210613.png)

分布式事务的执行流程：

1. TM 开启分布式事务（TM 向 TC 注册全局事务记录）；
2. 按业务场景，编排数据库、服务等事务内资源（RM 向 TC 汇报资源准备状态 ）；
3. TM 结束分布式事务，事务一阶段结束（TM 通知 TC 提交/回滚分布式事务）；
4. TC 汇总事务信息，决定分布式事务是提交还是回滚；
5. TC 通知所有 RM 提交/回滚 资源，事务二阶段结束。

**AT模式如何做到对业务的无侵入**

在一阶段，Seata 会拦截 **业务 SQL** ，

1. 解析 SQL 语义，找到 **业务 SQL** 要更新的业务数据，在业务数据被更新前，将其保存成 **before image** ;
2. 执行 **业务 SQL** 更新业务数据，在业务数据更新之后，
3. 其保存成 **after image**，最后生成行锁。

以上操作全部在一个数据库事务内完成，这样保证了一阶段操作的原子性。

![image-20220829221333370](http://image.xiaobailx.top/images/202208292213474.png)

二阶段如是顺利提交的话，因为“业务 SQL”在一阶段已经提交至数据库，所以Seata框架只需 **将一阶段保存的快照数据和行锁删掉，完成数据清理即可。**

![image-20220829221430273](http://image.xiaobailx.top/images/202208292214362.png)

二阶段如果是回滚的话，Seata 就需要回滚一阶段已经执行的 **业务 SQL** ，还原业务数据。回滚方式便是用 **before image** 还原业务数据；但在还原前要首先要校验脏写，对比 **数据库当前业务数据** 和  **after image** ，如果两份数据完全一致就说明没有脏写，可以还原业务数据，如果不一致就说明有脏写，出现脏写就需要转人工处理。

![image-20220829221611030](http://image.xiaobailx.top/images/202208292216133.png)

源码逻辑：

![image-20220829222715554](http://image.xiaobailx.top/images/202208292227653.png)
