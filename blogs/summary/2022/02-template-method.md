---
title: 模板方法模式总结
date: 2022-08-20
tags:
	- 设计模式
category: 
	- 学习总结
---

# 模板方法模式

近期在探究 Android 源码时，发现 Android 里面用到了大量的钩子方法，下意识反应这是一种设计模式的应用——模板方法，于是重新翻阅了刘伟老师的《Java设计模式》，趁着摸鱼时间对该设计模式做个简单总结。

举个现实生活中的例子，做便当（我每天做）。通常情况下，做便当的步骤基本比较固定：**准备食材 —》处理食材 —》完成打包** 。在这过程中，购买食材和打包的过程大同小异，而对食材的处理过程则存在巨大差异，而在软件开发中同样存在类似问题，这就衍生出了我们今天介绍的设计模式——模板方法模式。



## 模式简介

模板方法模式：定义一个操作中算法的框架，而将一些步骤延迟到子类中。模板方法模式使得一个子类可以不改变一个算法的结构即可重定义该方法的某些特定步骤。

![](http://image.xiaobailx.top/images/202209182214862.png)

模板方法是最简单的 **行为型** 设计模式，在其结构中只存在父类和子类之间的继承关系。其只有两个角色：抽象类和具体类。

1. 抽象类（AbstractClass）：定义了一系列的基本操作，子类可以重写这些基本方法；另外还实现了一个模板方法。
2. 具体类（ConcreteClass）：继承抽象类，实现抽象类的抽象基本操作，另外也可以重写抽象类中实现的基本方法。



## 实现说明

在模板方法模式中，抽象类负责描述一个算法的整体框架和流程，其子类负责具体过程实现。抽象类中定义的方法为基本方法，实现算法的逻辑步骤，而将基本步骤汇总起来的方法称之为模板方法，该模式的名字也因此而来。

**模板方法**

一个具体方法，由抽象类声明和实现，并被子类完成继承下来，实现了算法逻辑框架。由于模板方法是具体方法，因此模板方法模式的抽象层只能是抽象层。

**基本方法**

基本方法可以分为抽象方法、具体方法和钩子方法。其中抽象方法由抽象类声明、具体子类实现；具体方法可由抽象类或具体类声明和实现，子类可重写具体方法。钩子方法可由抽象类或具体类声明和实现，子类可重写钩子方法。

在模板方法模式中，钩子方法通常有两种实现方式，一种是实现体为空，另外是作为具体步骤的实现的判断条件，称之为 "挂钩"。这类钩子方法通常返回类型为 boolean 类型，方法名一般为 isXXX()，如：

```java
//模板方法
public void templateMethod(){
    step1();
    step2();
    //通过钩子方法判断是否执行下一步
    if(isToNext){ step3(); }
}

//钩子方法
public boolean isToNext(){
    return true;
}
```



## 应用实例

![image-20220911214403199](http://image.xiaobailx.top/images/202209112144269.png)

1. 抽象类——Bento

    ```java
    /**
     * 准备便当：
     * 准备食材 —》处理食材 —》完成打包
     */
    public abstract class Bento {

        protected String bentoName;

        protected String[] ingredients;

        public Bento(String bentoName,String... ingredients){
            this.bentoName = bentoName;
            this.ingredients = ingredients;
            System.out.println("做一份"+bentoName+"便当");
        }

        //模板方法
        public void make(){
            //1.准备食材
            prepare(ingredients);
            //2.处理食材
            handling();
            //3.完成打包
            if(isPackaging()) {
                packaging(bentoName);
            }
        }

        /**
         * 准备食材
         */
        public void prepare(String... Ingredients){
            System.out.println( "准备："+ Arrays.toString(Ingredients));
        }

        /**
         * 处理食材
         */
        public abstract void handling();

        /**
         * 完成打包
         */
        public void packaging(String bentoName){
            System.out.println("打包：" + bentoName);
        }
    
        //钩子方法
        public boolean isPackaging(){
            return true;
        }
    }
    ```

2. 具体类——BraisedChicken、LettuceWithOyster、TomatoWithEgg

   - BraisedChicken

   ```java
   /**
    * 黄焖鸡腿
    * */
   public class BraisedChicken extends Bento{
   
       public BraisedChicken() {
           super("黄焖鸡腿", "鸡腿","生姜","香菇","料酒","盐");
       }
        
       @Override
    public void handling() {
           System.out.println("处理：1.腌制鸡腿");
           System.out.println("     2.加入料酒、生姜、香菇焖煮");
           System.out.println("     3.加盐调味关火");
    }
   }
   ```

   - LettuceWithOyster

   ```java
   /**
    * 蚝油生菜
    * */
   public class LettuceWithOyster extends Bento{

       public LettuceWithOyster() {
           super("蚝油生菜", "生菜","蚝油","大蒜","鲜味汁","盐");
       }

       @Override
       public void handling() {
           System.out.println("处理：1.焯生菜");
           System.out.println("     2.炒蒜末");
           System.out.println("     3.蚝油、鲜味汁加盐调汁");
           System.out.println("     4.大火收汁关火");
           System.out.println("     5.生菜淋调味汁");
       }

       //重写钩子方法
       @Override
       public boolean isPackaging(){
           return false;
       }
   }
   ```
   
   - TomatoWithEgg
   
   ```java
   /**
    * 西红柿炒鸡蛋
    * */
   public class TomatoWithEgg extends Bento{
   
       public TomatoWithEgg() {
           super("西红柿炒鸡蛋", "西红柿","鸡蛋","糖","盐");
       }
   
       @Override
       public void handling() {
           System.out.println("处理：1.炒鸡蛋");
           System.out.println("     2.炒西红柿");
           System.out.println("     3.加糖和盐");
           System.out.println("     4.收汁关火");
       }
   }
   ```

3. 客户端

   ```java
   public class MainTest {
   
       public static void main(String[] args) {
           //做份西红柿炒蛋便当
           Bento bento01 = new TomatoWithEgg();
           bento01.make();
   
           //做份蚝油生菜便当
           Bento bento02 = new LettuceWithOyster();
           bento02.make();
   
           //做份黄焖鸡腿便当
           Bento bento03 = new BraisedChicken();
           bento03.make();
       }
   }
   ```

   

## 模式总结

**优点**

1. 使用父类形式化定义一个算法，子类处理细节实现，细节处理过程中不影响父类的定义的算法执行顺序。
2. 子类通过覆盖钩子方法可实现反向控制父类的算法执行步骤，具有良好的扩展性，符合单一职责和开闭原则。

**缺点**

由于每个具体方法不同实现都需要提供一个子类，如果父类中可变方法过多，将会导致类的数量增多，使系统变得庞大。

**应用场景**

1. 一次性实现一个算法的不变部分，并将可变行为留给子类实现。
2. 通过子类来决定父类算法中的某个步骤是可执行的，实现子类对父类的反向控制。



参考书籍：《Java设计模式》/ 刘伟 编著 | 清华大学出版社