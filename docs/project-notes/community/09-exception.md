---
title: 踩坑记录
date: 2022-01-17
tags:
 - 异常
categories:
 - Java项目
---


## Error creating bean with name 'dataSource'


SpringBoot 项目初始化时引入了数据源配置，在做项目基础测试时没有进行数据源配置，启动后出现如下提示：

```tex
***************************
APPLICATION FAILED TO START
***************************

Description:

Failed to configure a DataSource: 'url' attribute is not specified and no embedded datasource could be configured.

Reason: Failed to determine a suitable driver class


Action:

Consider the following:
	If you want an embedded database (H2, HSQL or Derby), please put it on the classpath.
	If you have database settings to be loaded from a particular profile you may need to activate it (no profiles are currently active).
```

查看详情：

```tex
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'dataSource' defined in class path resource [org/springframework/boot/autoconfigure/jdbc/DataSourceConfiguration$Hikari.class]: Bean instantiation via factory method failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [com.zaxxer.hikari.HikariDataSource]: Factory method 'dataSource' threw exception; nested exception is org.springframework.boot.autoconfigure.jdbc.DataSourceProperties$DataSourceBeanCreationException: Failed to determine a suitable driver class
```

大概意思是无法找到数据源，找具体原因和对应解决方法：

> **原因：**spring boot默认会加载 org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration 类，DataSourceAutoConfiguration类使用了@Configuration注解向spring注入了dataSource bean。因为工程中没有关于dataSource相关的配置信息，当spring创建dataSource bean因缺少相关的信息就会报错。
>
> **解决方法：**在Application类上增加：@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
>
> 原文链接：https://blog.csdn.net/q1035331653/article/details/80418026

再次测试，启动成功！

