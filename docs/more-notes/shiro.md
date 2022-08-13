---
title: Shiro
tags:
 - 后端技术
categories:
 - 框架学习
---

## 一、Shiro 概述

### 1. Shiro 简介

- [Apache Shiro](http://shiro.apache.org/) 是 Java 的一个安全（权限）框架。
- Shiro 可以非常容易的开发出足够好的应用，其不仅可以用在 JavaSE 环境，也可以用在 JavaEE 环境。
- Shiro 可以完成：认证、授权、加密、会话管理、与Web 集成、缓存等。

### 2. 功能简介

[官方文档](https://shiro.apache.org/introduction.html)

![image-20220116104454370](http://image.xiaobailx.top/images/20220116104502.png)



- Authentication：**身份认证/登录** ，验证用户是不是拥有相应的身份；
- Authorization：**授权，即权限验证 **，验证某个已认证的用户是否拥有某个权限；即判断用户是否能进行什么操作，如：验证某个用户是否拥有某个角色。或者细粒度的验证某个用户对某个资源是否具有某个权限；
- Session Manager：**会话管理** ，即用户登录后就是一次会话，在没有退出之前，它的所有信息都在会话中；**会话可以是普通 JavaSE 环境，也可以是 Web 环境的；**
- Cryptography：**加密 **，保护数据的安全性，如密码加密存储到数据库，而不是明文存储；
- Web Support：**Web 支持** ，可以非常容易的集成到Web 环境；
- Caching：**缓存 **，比如用户登录后，其用户信息、拥有的角色/权限不必每次去查，这样可以提高效率；
- Concurrency：Shiro 支持 **多线程应用的并发验证** ，即如在一个线程中开启另一个线程，能把权限自动传播过去；
- Testing：提供 **测试** 支持；
- Run As：**允许一个用户假装为另一个用户** （如果他们允许）**的身份进行访问** ；
- Remember Me：**记住我** ，这个是非常常见的功能，即一次登录后，下次再来的话不用登录了

### 3. Shiro 架构

(官方文档)[https://shiro.apache.org/architecture.html]

#### 3.1 高级概述

![image-20220116104840602](http://image.xiaobailx.top/images/20220116104840.png)



- Subject：**应用代码直接交互的对象是 Subject**，也就是说 Shiro 的对外API 核心就是 Subject。**Subject 代表了当前“用户”** ， 这个用户不一定是一个具体的人，与当前应用交互的任何东西都是 Subject，如网络爬虫，机器人等；**与 Subject 的所有交互都会委托给SecurityManager；Subject 其实是一个门面，SecurityManager 才是实际的执行者；**
-  SecurityManager：安全管理器；**即所有与安全有关的操作都会与 SecurityManager 交互** ；且其管理着所有 Subject；可以看出它是 Shiro的核心，它 **负责与 Shiro 的其他组件进行交互** ，它相当于 SpringMVC 中 DispatcherServlet 的角色
- Realm：Shiro **从 Realm 获取安全数据（如用户、角色、权限）** ，就是说 SecurityManager 要验证用户身份，那么它需要从 Realm 获取相应的用户进行比较以确定用户身份是否合法；也需要从 Realm 得到用户相应的角色权限进行验证用户是否能进行操作；可以把 Realm 看成 DataSource



#### 3.2 详细架构

![image-20220116105946634](http://image.xiaobailx.top/images/20220116105946.png)

- Subject：任何可以与应用交互的“用户”；
- SecurityManager ：相当于SpringMVC 中的 DispatcherServlet；是 Shiro 的心脏；所有具体的交互都通过 SecurityManager 进行控制；它管理着所有 Subject、且负责进行认证、授权、会话及缓存的管理。
- Authenticator：**负责 Subject 认证** ，是一个扩展点，可以自定义实现；可以使用认证策略（Authentication Strategy），即什么情况下算用户认证通过了；
- Authorizer：**授权器** 、即访问控制器，用来决定主体是否有权限进行相应的操作；即控制着用户能访问应用中的哪些功能；
- Realm：可以有 1 个或多个 Realm，可以认为是安全实体数据源，即用于获取安全实体的；可以是JDBC 实现，也可以是内存实现等等；由用户提供；所以一般在应用中都需要实现自己的Realm；
- SessionManager：**管理 Session 生命周期的组件**；而 Shiro 并不仅仅可以用在 Web 环境，也可以用在如普通的 JavaSE 环境；
- CacheManager：**缓存控制器** ，来管理如用户、角色、权限等的缓存的；因为这些数据基本上很少改变，放到缓存中后可以提高访问的性能；
- Cryptography：**密码模块** ，Shiro 提高了一些常见的加密组件用于如密码加密/解密。



##  二、HelloWorld



## 三、集成 Spring



## 四、认证





## 五、授权

- 授权：也叫 **访问控制，即在应用中控制谁访问哪些资源**（如访问页面/编辑数据/页面操作等）。在授权中需了解的几个关键对象：主体（Subject）、资源（Resource）、权限（Permission）、角色（Role）。
- 主体(Subject)：访问应用的用户，在 Shiro 中使用 Subject 代表该用户。用户只有授权后才允许访问相应的资源。
- 资源(Resource)：**在应用中用户可以访问的 URL** ，比如访问 JSP 页面、查看/编辑某些数据、访问某个业务方法、打印文本等等都是资源。用户只要授权后才能访问。
- 权限(Permission)：安全策略中的原子授权单位，通过权限我们可以表示在应用中用户有没有操作某个资源的权力。即 **权限表示在应用中用户能不能访问某个资源** ，如：访问用户列表页面查看/新增/修改/删除用户数据（即很多时候都是CRUD（增查改删）式权限控制）等。权限代表了用户有没有操作某个资源的权利，即反映在某个资源上的操作允不允许。
- Shiro 支持粗粒度权限（如用户模块的所有权限）和细粒度权限（操作某个用户的权限，即实例级别的）
- 角色(Role)：**权限的集合**，一般情况下会赋予用户角色而不是权限，即这样用户可以拥有一组权限，赋予权限时比较方便。典型的如：项目经理、技术总监、CTO、开发工程师等都是角色，不同的角色拥有一组不同的权限。

### 1. 授权方式

Shiro 支持三种方式的授权：

- 编程式：通过写 if/else 授权代码块完成；

```java
if(subject.hasRole("admin")){
//拥有权限
}else{
//未获取权限
}
```

  

- 注解式：通过在执行的Java方法上放置相应的注解完成，没有权限将抛出相应的异常；

```java
@RequiresRoles("admin")
public void hello(){
	//拥有权限
}
```

 

- JSP/GSP 标签：在JSP/GSP 页面通过相应的标签完成

```java
<shiro:hasRole name = "admin">
	<!-拥有权限->
</shiro:hasRole>
```



###  2. 默认拦截器



### 3. Permissions

- 规则： **资源标识符 : 操作 : 对象实例ID** ，即对哪个资源的哪个实例可以进行什么操作. 其 **默认支持通配符权限字符串**。" **:** " 表示 **资源/操作/实例的分割** ；" **,** " 表示 **操作的分割** ；" ***** " 表示 **任意资源/操作/实例**。
- 多层次管理
  - 例如：user:query、user:edit；
    - " **:** " 是一个特殊字符，它用来分隔权限字符串的下一 部件：第一部分是权限被操作的领域（打印机），第二部分是被执行的操作。
    - 多个值： **每个部件能够保护多个值** 。因此，除了授予用户 user:query 和 user:edit 权限外，也可以简单地授予他们一 个： user: query , edit
    - 还可以用 " ***** " 号代替所有的值，如：user:* ， 也可以写：*:query，表示某个用户在所有的领域都有 query 的权限

### 4. Shiro Permissions

实例级访问控制

- 这种情况通常会使用三个部件： 域、操作、被付诸实施的实例。如：```user:edit:manager```
- 也 可以使用通配符来定义，如：```user:edit:*```  、```user: * :*``` *、```user:*:manager```*
- 部分省略通配符：缺少的部件意味着用户可以访问所有与之匹配的值，比如：```user:edit``` 等价于 ```user:edit :*``` 、
  user 等价于 ```user:*:*```
- 注意： 通配符只能从字符串的结尾处省略部件，也就是说 user:edit 并不等价于 user:*:edit

### 5. 授权流程

流程如下：

1. 首先调用 ```Subject.isPermitted*/hasRole* ``` 接口，其会委托给 SecurityManager，而 SecurityManager 接着会委托给 Authorizer；
2. Authorizer是真正的授权者，如调用 ```isPermitted("user:view")```，其首先会通过 PermissionResolver 把字符串转换成相应的 Permission 实例；
3. 在进行授权之前，其会调用相应的 Realm 获取 Subject 相应的角色/权限用于匹配传入的角色/权限；
4. Authorizer 会判断 Realm 的角色/权限是否和传入的匹配，如果有多个 Realm，会委托给 ModularRealmAuthorizer 进行循环判断，
   如果匹配如 ```isPermitted*/hasRole* ```会返回 true，否则返回 false 表示授权失败。



## 六、会话管理



## 七、缓存





## 八、RememberMe