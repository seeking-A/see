---
title: Spring入门
date: 2022-01-15
tags:
 - 仿牛客社区
categories:
 - Java项目
---

## Bean 的管理

```tex
@SpringBootApplication

@SpringBootConfiguration

@EnableAutoConfiguration

@ComponentScan

@Repository

@Service

@Controller
```



## 测试类中使用 Spring 容器

```java
@RunWith(SpringRunner.class)
@SpringBootTest
//启动配置类
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
//实现ApplicationContextAware接口使用Spring容器
class CommunityApplicationTests implements ApplicationContextAware {

    @Test
    void contextLoads() {
    }

    private ApplicationContext applicationContext;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.applicationContext = applicationContext;
    }
}
```



### AOP 应用 @PostConstruct、 @PreDestroy

```java
@Service
public class AlphaService {

    public AlphaService(){
        System.out.println("实例化AlphaService");
    }

    /**
     * 执行构造函数后执行
     **/
    @PostConstruct
    public void init(){
        System.out.println("初始化AlphaService");
    }

    /**
     * 对象销毁前执行
     **/
    @PreDestroy
    public void destory() {
        System.out.println("销毁AlphaService");
    }
}

```



### 配置优先级 @Primary

```java
@Repository()
@Primary
public class AlphaDaoMyBatisImpl implements AlphaDao {
    @Override
    public String select() {
        return "MybatisImpl";
    }
}
```



### 自定义组件 @Bean

```java
@Configuration
public class AlphaConfig {

    @Bean
    public SimpleDateFormat simpleDateFormat() {
        return new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    }

}
```