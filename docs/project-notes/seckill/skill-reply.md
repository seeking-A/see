---
title: 秒杀复盘
date: 2022-02-16
tags:
 - 电商秒杀
categories:
 - Java项目
---

### 技术栈

前端：Thymeleaf + Bootstrap + Jquery 

后端：SpringBoot + Mybatis + Lombok

中间件： Redis + RabbitMQ



### 实现流程

1. 搭建项目

   1. 修改配置文件：Thymeleaf、数据源、线程池、Mybatis-plus、Logger
   2. 添加公共返回类

2. 分布式会话

   1. 实现登入功能：双重MD5加密、将用户信息放到Session中
   2. 参数校验：引入validation、定义校验手机号验证规则、定义手机校验注解、为LoginVO属性添加校验

3. 异常处理

   1. 全局异常处理：使用@ControllerAdvice和@ExceptionHandler注解、使用ErrorController类

4. 分布式Session

   > （Nginx 使用默认负载均衡策略（轮询），请求将会按照时间顺序逐一分发到后端应用上

   1. 使用Cookie+Session保存User信息
   2. 实现分布式Session：
      1. 使用SpringSesion实现：引入session-data-redis依赖
      2. 将用户信息存入到Redis：引入data-redis依赖、编写RdisConfig、优化登入功能

5. 秒杀功能

   1. 商品列表首页
   1. 商品详情页
   1. 秒杀功能实现

6. 系统压测

7. 页面优化

   1. 缓存商品列表页、商品详情页、订单详情页静态化

8. 接口优化

   1. 预加载库存、Redis操作库存、客户端轮训查询
   2. 使用Lua脚本实现分布式锁：编写Lua表达式、添加Lua脚本配置、调用脚本

9. 安全优化

   1. 隐藏秒杀地址
   2. 添加图形验证码：引入easy-captcha依赖、生成验证码依赖、验证码验证
   3. 接口限流
      1. 简单限流：使用计数器方式实现
      2. 通用限流：自定义注解@AccessLimit、实现HandlerInterceptor接口PreHandle方法、在MVC配置类中注册拦截器