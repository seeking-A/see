---
title: 注解和拦截器
date: 2022-01-15
tags:
 - 仿牛客社区
categories:
 - Java项目
---

## 注解

### 常用元注解

```tex
@Retention：用于指定该 Annotation 的生命周期，包含一个 RetentionPolicy 类型的成员变量；
@Target：用于指定被修饰的 Annotation 能用于修饰哪些程序元素，包含一个名为 value 的成员变量；
@Documented：修饰的 Annotation 类将被 javadoc 工具提取成文档，javadoc 默认不包括该注解，定义为Documented 的注解必须设置Retention值为 RUNTIME；
@Inherited: 被它修饰的 Annotation 将具有继承性。如果某个类使用了被 @Inherited 修饰的 Annotation, 则其子类将自动具有该注解。
```



### 自定义注解

```java
//没有成员定义的 Annotation 称为 标记; 包含成员变量的 Annotation 称为元数据 Annotation
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
//使用 @interface 关键字
@interface DefAnnotation {
    /**
     * 成员变量在 Annotation 定义中以无参数方法的形式来声明。
     * 其方法名和返回值定义了该成员的名字和类型。我们称为配置参数。
     * 类型只能是八种基本数据类型、String、Class、enum、Annotation 以上所有类型的数组。
     **/
    int max();

    //指定成员变量的初始值可使用 default关键字
    //如果只有一个参数成员，建议使用参数名为value
    String defInfo() default "自定义注解";
}
```



### 读取注解

```tex
//获取在此元素上所有的Annotation
Method.getDeclaredAnnotations()
//获取指定类型的Annotation
Method.getAnnotation(Class<T> annotationClass)
```



## 拦截器

```java
public interface HandlerInterceptor {

	// 业务处理器处理请求之前执行
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception { }

    // 业务处理器处理请求之后执行
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception { }

    // DispatcherServlet处理完请求后执行
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {  }
```



### 实现登入拦截

#### 1. 定义注解

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface LoginRequired {

}
```



#### 2. 实现拦截器

```java
@Component
public class LoginRequiredInterceptor implements HandlerInterceptor {

    @Autowired
    private HostHolder hostHolder;

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        if (handler instanceof HandlerMethod) {
            HandlerMethod handlerMethod = (HandlerMethod) handler;
            Method method = handlerMethod.getMethod();
            LoginRequired loginRequired = method.getAnnotation(LoginRequired.class);
            if (loginRequired != null && hostHolder.getUser() == null) {
                response.sendRedirect(request.getContextPath() + "/login");
                return false;
            }
        }
        return true;
    }
}
```



#### 3. 配置拦截器

```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    @Autowired
    private LoginTicketInterceptor loginTicketInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {

        registry.addInterceptor(loginRequiredInterceptor)
                .excludePathPatterns("/**/*.css", "/**/*.js", "/**/*.png", "/**/*.jpg", "/**/*.jpeg");
    }

}
```



#### 4. 使用注解

```java
@LoginRequired
@RequestMapping(path = "/setting", method = RequestMethod.GET)
public String getSettingPage() {
    return "/site/setting";
}
```