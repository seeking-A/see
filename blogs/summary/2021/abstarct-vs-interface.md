---
title: 抽象类和接口的区别
date: 2021-12-15
tags:
 - Java
categories:
 - 复盘
---

### 抽象类和接口的区别

含有abstract修饰符的class即为抽象类，**abstract 类不能创建实例对象**。含有abstract方法的类必须定义为abstract class，abstract class类中的方法不必是抽象的。**abstract class类中定义抽象方法必须在具体(Concrete)子类中实现**，所以，不能有抽象构造方法或抽象静态方法。**如果子类没有实现抽象父类中的所有抽象方法，那么子类也必须定义为abstract类型。**

接口（interface）可以说成是抽象类的一种特例，接口中的所有方法都必须是抽象的。**接口中的方法定义默认为public abstract类型**，接口中的成员变量类型默认为public static final。

下面比较一下两者的**语法区别**：

1. 抽象类可以有构造方法，接口中不能有构造方法。

2. 抽象类中可以有普通成员变量，接口中没有普通成员变量。

3. 抽象类中可以包含非抽象的普通方法，接口中的所有方法必须都是抽象的，不能有非抽象的普通方法。
4. 抽象类中的抽象方法的访问类型可以是public，protected和（默认类型,虽然eclipse下不报错，但应该也不行），但接口中的抽象方法只能是public类型的，并且默认即为public abstract类型。
5. 抽象类中可以包含静态方法，接口中不能包含静态方法（Java1.8之前）。
6. 抽象类和接口中都可以包含静态成员变量，抽象类中的静态成员变量的访问类型可以任意，但接口中定义的变量只能是public static final类型，并且默认即为public static final类型。
7. 一个类可以实现多个接口，但只能继承一个抽象类。

接着再说说两者在**应用上的区别**：

接口更多的是在系统架构设计方法发挥作用，**主要用于定义模块之间的通信契约**。而抽象类在代码实现方面发挥作用，可以**实现代码的重用**，例如，**模板方法设计模式是抽象类的一个典型应用**，假设某个项目的所有 Servlet 类都要用相同的方式进行权限判断、记录访问日志和处理异常，那么就可以定义一个抽象的基类，让所有的Servlet都继承这个抽象基类，在抽象基类的 service 方法中完成权限判断、记录访问日志和处理异常的代码，在各个子类中只是完成各自的业务逻辑代码，伪代码如下：

```java
public abstract class BaseServlet extends HttpServlet{

    public final void service(HttpServletRequest request, HttpServletResponse response) throws 		 IOExcetion,ServletException{
        记录访问日志
        进行权限判断

        if(具有权限){
             try{
                 doService(request,response);
             } catch(Excetpion e) {
                 记录异常信息
             }
        }

    }

    protected abstract void doService(HttpServletRequest request, HttpServletResponse response) throws IOExcetion,ServletException; 
    //注意访问权限定义成protected，显得既专业，又严谨，因为它是专门给子类用的
    }


public class MyServlet1 extends BaseServlet {

	protected void doService(HttpServletRequest request, HttpServletResponse response) throws IOExcetion,ServletException {
            本Servlet只处理的具体业务逻辑代码
    }

}

```

父类方法中间的某段代码不确定，留给子类干，就用模板方法设计模式。

备注：这道题的思路是先从总体解释抽象类和接口的基本概念，然后再比较两者的语法细节，最后再说两者的应用区别。比较两者语法细节区别的条理是：先从一个类中的构造方法、普通成员变量和方法（包括抽象方法），静态变量和方法，继承性等6个方面逐一去比较回答，接着从第三者继承的角度的回答，特别是最后用了一个典型的例子来展现自己深厚的技术功底。

---

### 抽象类和普通类的区别

1.  抽象类不能被实例化。
2.  抽象类可以有构造函数，被继承时子类必须继承父类一个构造方法，抽象方法不能被声明为静态。
3.  抽象方法只需申明，而无需实现，抽象类中可以允许普通方法有主体

4.  含有抽象方法的类必须申明为抽象类

5.  抽象的子类必须实现抽象类中所有抽象方法，否则这个子类也是抽象类。

---

### 总结

- ##### 抽象类和接口的区别

|              抽象类              |                 接口                  |
| :------------------------------: | :-----------------------------------: |
|            有构造函数            |              无构造函数               |
| 和普通类一样可以有自己的成员变量 | 成员变量只可以public static final修饰 |
|   除了抽象方法还可以有普通方法   |      只有抽象方法（Java1.8之前）      |
|       一个类只能继承一个类       |        一个类可以实现多个接口         |



- #### 抽象类和普通类的区别

|                            抽象类                            |                普通类                |
| :----------------------------------------------------------: | :----------------------------------: |
|                          不能实例化                          |              可以实例化              |
|         抽象类中的抽象方法只有方法声明，没有方法实现         | 普通类中不能只声明方法,方法必须实现  |
|    抽象类中不一定有抽象方法，但有抽象方法的类一定是抽象类    |       普通类中不可能有抽象方法       |
| 子类继承抽象类之后，必须覆盖父类当中所有的抽象方法，否则子类还是抽象类 | 子类继承父类方法可以重写也可以不重写 |

