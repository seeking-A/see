---
title: 枚举类和注解
date: 2020-10-09
tags:
 - Java
categories:
 - Java基础
---

## 枚举类

### 枚举类的实现

1. JDK1.5之前需要自定义枚举类;
2. JDK 1.5 新增的 enum 关键字用于定义枚举类.



### 枚举类的属性

1. 枚举类对象的属性不应允许被改动, 所以应该使用 private final 修饰；
2. 枚举类的使用 private final 修饰的属性应该在构造器中为其赋值；
3. 若枚举类显式的定义了带参数的构造器, 则在列出枚举值时也必须对应的传入参数。



### 自定义枚举类

           1. 私有化类的构造器，保证不能在类的外部创建其对象；
           2. 在类的内部创建枚举类的实例。声明为：public static final；
           3. 对象如果有实例变量，应该声明为private final，并在构造器中初始化。

```java
//自定义枚举类
class Season{
    private final String SEASONNAME;//季节的名称
    private final String SEASONDESC;//季节的描述
    private Season(String seasonName,String seasonDesc){
        this.SEASONNAME = seasonName;
        this.SEASONDESC = seasonDesc;
    }
    public static final Season SPRING = new Season("春天", "春暖花开");
    public static final Season SUMMER = new Season("夏天", "夏日炎炎");
    public static final Season AUTUMN = new Season("秋天", "秋高气爽");
    public static final Season WINTER = new Season("冬天", "白雪皑皑");
}
```



### 使用enum定义枚举类

1. 使用 enum 定义的枚举类默认继承了 java.lang.Enum类，因此不能再继承其他类；
2. 枚举类的构造器只能使用 private 权限修饰符
3. 枚举类的所有实例必须在枚举类中显式列出(, 分隔 ; 结尾)。列出的实例系统会自动添加 public static final 修饰；
4. 必须在枚举类的第一行声明枚举类对象。

```java
//使用enum定义枚举类
enum SeasonEnum {
    SPRING("春天","春风又绿江南岸"),
    SUMMER("夏天","映日荷花别样红"),
    AUTUMN("秋天","秋水共长天一色"),
    WINTER("冬天","窗含西岭千秋雪");
    private final String seasonName;
    private final String seasonDesc;
    private SeasonEnum(String seasonName, String seasonDesc) {
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }
    public String getSeasonName() {
        return seasonName;
    }
    public String getSeasonDesc() {
        return seasonDesc;
    }
}
```



### Enum类的主要方法

    1. values() 方法：返回枚举类型的对象数组。该方法可以很方便地遍历所有的枚举值；
    2. valueOf(String str)：可以把一个字符串转为对应的枚举类对象。要求字符串必须是枚举类对象的“名字”。如果不是，会有运行时异常：IllegalArgumentException。
    3. toString()：返回当前枚举类对象常量的名称



### 实现接口的枚举类

​    情况一：实现接口，在enum类中实现抽象方法
​    情况二：让枚举类的对象分别实现接口中的抽象方法

```java
//实现接口的枚举类
interface Info{
    public void show();
}

enum SeasonEnums implements Info{
    SPRING("春天","春风又绿江南岸"){
        @Override
        public void show() {
            System.out.println("春天在哪里");
        }
    },
    SUMMER("夏天","映日荷花别样红"){
        @Override
        public void show() {
            System.out.println("宁夏");
        }
    },
    AUTUMN("秋天","秋水共长天一色"){
        @Override
        public void show() {
            System.out.println("秋天不回来");
        }
    },
    WINTER("冬天","窗含西岭千秋雪"){
        @Override
        public void show() {
            System.out.println("大约在冬季");
        }
    };
    private final String seasonName;
    private final String seasonDesc;
    private SeasonEnums(String seasonName, String seasonDesc) {
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }


    public String getSeasonName() {
        return seasonName;
    }
    public String getSeasonDesc() {
        return seasonDesc;
    }

    //实现接口，在enum类中实现抽象方法
    @Override
    public void show(){
        System.out.println("又是一个季节");
    }
}
```



## 注解

### Annotation 概述

- JDK 5.0 增加了对元数据(MetaData) 的支持，即 Annotation (注解)。
- Annotation 其实就是代码里的特殊标记, 这些标记可以在编译、 类加载、运行时被读取, 并执行相应的处理。
- Annotation 可以像修饰符一样被使用, 可用于 修饰包、类、构造器、方法、成员变量、参数, 局部变量的声明, 这些信息被保存在 Annotation 的 “name=value” 对中。
- 在JavaEE/Android中注解占据了更重要的角色，例如用来配置应用程序的任何切面，代替JavaEE旧版中所遗留的繁冗代码和XML配置等。
- 注解是一种趋势，一定程度上可以说：**框架 = 注解 + 反射 + 设计模式** 。



### Annotation  常见示例

使用 Annotation 时要在其前面增加 @ 符号, 并 **把该 Annotation 当成一个修饰符使用**，用于修饰它支持的程序元素。

#### 示例一：生成文档相关的注解

```tex
  /**
    * @author 标明开发该类模块的作者，多个作者之间使用,分割
    * @version 标明该类模块的版本
    * @see 参考转向，也就是相关主题
    * @since 从哪个版本开始增加的
    * @param 对方法中某参数的说明，如果没有参数就不能写
    * @return 对方法返回值的说明，如果方法的返回值类型是void就不能写
    * @exception 对方法可能抛出的异常进行说明 ，如果方法没有用throws显式抛出的异常就不能写
    * 其中
    * @param @return 和 @exception 这三个标记都是只用于方法的。
    * @param的格式要求：@param 形参名 形参类型 形参说明
    * @return 的格式要求：@return 返回值类型 返回值说明
    * @exception的格式要求：@exception 异常类型 异常说明
    * @param和@exception可以并列多个
    **/
```



#### 示例二： JDK 内置的三个基本注解

```tex
   /**
     * @Override: 限定重写父类方法, 该注解只能用于方法
     * @Deprecated: 用于表示所修饰的元素(类, 方法等)已过时。通常是因为所修饰的结构危险或存在更好的选择
     * @SuppressWarnings: 抑制编译器警告
     **/
```



####  示例三： 跟踪代码依赖性

```java
@WebServlet("/login")
public class LoginServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws
            ServletException, IOException { }
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws
            ServletException, IOException {
        doGet(request, response);
    } 
}
```



### 自定义 Annotation

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



####JDK 中的元注解

JDK 的元 Annotation 用于修饰其他 Annotation 定义。

DK5.0提供了4个标准的meta-annotation类型，分别是：

```tex
@Retention：用于指定该 Annotation 的生命周期，包含一个 RetentionPolicy 类型的成员变量；
@Target：用于指定被修饰的 Annotation 能用于修饰哪些程序元素，包含一个名为 value 的成员变量；
@Documented：修饰的 Annotation 类将被 javadoc 工具提取成文档，javadoc 默认不包括该注解，定义为Documented 的注解必须设置Retention值为 RUNTIME；
@Inherited: 被它修饰的 Annotation 将具有继承性。如果某个类使用了被 @Inherited 修饰的 Annotation, 则其子类将自动具有该注解。
```

