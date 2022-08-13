---
title: 社区复盘
date: 2022-02-01
tags:
 - 仿牛客社区
categories:
 - Java项目

---

[Java牛客网社区项目——知识点&面试题](https://blog.csdn.net/weixin_48610702/article/details/115859656)

### 技术栈

前端： Thymeleft

后端： SpringBoot + MyBatis + SpringSecurity

中间件： Redis + Kafak + Elasticsearch

### 实现流程

1. 环境搭建
   1. 修改配置文件：Thymeleaf、数据源、Mybatis-Plus、Logger 
2. 开发社区首页

   1. 对帖子进行封装（点缀信息、用户信息），完成首页信息展示
   2. 添加分页工具类，添加分页功能
   3. 添加日志记录
3. 开发社区登录模块

   1. 发送邮件功能
      1. 邮箱设置：启用客户端SMTP服务
      2. 引入 Spring Email，配置 Spring Email
      3. 添加 MailClient 工具类，使用 JavaMailSender
      4. 使用 Thymeleaf 模板引擎生成一个 html 文件作为邮件内容

   2. 开发注册功能
      1. 跳转注册页面
      2. 表单数据校验、发送激活邮箱

   3. 开发登入功能
      1. 跳转登录页面
      2. 添加 UUID 工具类，实现登录表单校验

   4. 会话管理
      1. 添加 LoginTicket 作为登录凭证
      2. 使用 Redis 对登录信息进行存储
      3. 将登录信息保存到 Cookies 中
   5. 生成验证码
      1. 引入 Kaptcha、编写 Kaptcha 配置类
      2. 生成数据字符串、生成图片
      3. 完善登录接口
   6. 登录、登出功能
      1. 完善登录接口、实现登出功能
      2. 定义拦截器 LoginTicket，注册拦截器

   7. 账号设置
      1. 文件上传
      2. 修改密码
   8. 登录状态校验
      1. 自定义注解 @LoginRequired 用于登录状态校验
      2. 自定义拦截器 LoginRequiredInterceptor 并注册拦截器
4. 开发社区核心功能

   1. 敏感词过滤
      1. 编写 SesitiveFilter 工具类，构建前缀树
   2. 发布帖子
      1. 实现帖子内容的过滤和添加
   3. 显示评论
   4. 添加评论
   5. 发送私信
   6. 统一异常处理
      1. 实现 ExceptionAdvice 类，使用 @ControllerAdvice 和 @ExceptionHandler 注解
   7. 统一记录日志
      1. 实现 ServiceLogAspect 类，使用 @Component 和 @Aspect
5. 高性能存储方案
   1. 引入 data-redis 依赖、修改配置文件
   2. 编写 RedisConfig 配置类，构造 RedisTemplate
   3. 点赞功能
      1. 使用 set 数据类型实现
      2. 使用编程式事务重构，获取用户总赞数
   4. 关注列表、粉丝列表
   5. 使用 Redis 优化登入模块
6. 异步消息系统
   1. 通知列表：显示评论、点赞、关注三类通知
   2. 通知详情：分页显示某一类主题所包含的通知
   3. 未读消息：在页面头部显示所有未读消息数量
7. 分布式搜索
   1. 发帖时，使用 Kafaka 消息中间件将帖子消息存入到 Elasticsearch 服务器中
   2. 删帖时，使用 Kafaka 消息中间件将帖子消息从 Elasticsearch 服务器中移除

8. 企业服务
   1. UAD：使用 BigMap 数据类型记录网站访问量
   2. UV：使用 HyperLogLog 记录用户访问量



