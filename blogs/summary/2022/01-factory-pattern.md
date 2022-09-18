---
title: 工厂模式总结——三个工厂
date: 2022-08-20
tags:
	- 设计模式
category: 
	- 学习总结
---



在创建型模式中，工厂模式是我们日常使用最为频繁的设计模式之一。工厂模式可细分为简单工厂模式、工厂方法模式、抽象工厂模式。



## 简单工厂模式

### 模式简介

> 简单工厂模式：根据参数返回不同类的实例，这些类通常具有共同的父类。

![](http://image.xiaobailx.top/images/202209111759479.png)

简单工厂模式包括三个角色：

1. 工厂 (Factory)：用于创建所需产品，提供静态工厂方法，返回抽象产品类型 Product；
2. 抽象产品 (Product)：是工厂创建的所有对象的父类，封装了各种共有方法；
3. 具体产品 (ConcreateProduct)：工厂创建目标，继承抽象产品，实现了抽象产品的抽象方法。



### 应用实例

![image-20220911203830271](http://image.xiaobailx.top/images/202209112038330.png)

1. 工厂——FruitFactory

   ```java
   public class FruitFactory {
   
       public static Object createFruit(String clazzName)  throws Exception{
           Object obj = null;
           if (clazzName.equals(Apple.class.getName())){
               obj = new Apple();
           } else if (clazzName.equals(Strawberry.class.getName())){
               obj = new Strawberry();
           } else if (clazzName.equals(Banana.class.getName())){
               obj = new Banana();
           } else {
               throw new ClassNotFoundException("没有找到该类");
           }
           return obj;
       }
   
       //升级简化版
       /*public static Object create(String clazzName) throws Exception{
           Object obj = null;
           Class clazz = Class.forName(clazzName);
           if (clazz.isAssignableFrom(Fruit.class)){
               obj = clazz.newInstance();
           } else {
               throw new ClassNotFoundException("没有找到该类");
           }
           return obj;
       }*/
   }
   ```

2. 抽象产品——Fruit

   ```java
   public abstract class Fruit {
       public abstract void getInfo();
   }
   ```

3. 具体产品——Apple、Banana、Strawberry

   - Apple

   ```java
   public class Apple extends Fruit{
       @Override
       public void getInfo() {
           System.out.println("这是一个砸中牛顿脑袋的苹果");
       }
   }
   ```

   - Banana

   ```java
   public class Banana extends Fruit{
       @Override
       public void getInfo() {
           System.out.println("这是一根让犹太人致富的香蕉");
       }
   }
   ```

   - Strawberry

   ```java
   public class Strawberry extends Fruit{
       @Override
       public void getInfo() {
           System.out.println("这是一颗甜到你心里的草莓");
       }
   }
   ```

4. 客户端

   ```java
   public class MainTest {
       public static void main(String[] args) throws Exception {
           //要个苹果
           Apple apple = (Apple) FruitFactory.createFruit(Apple.class.getName());
           apple.getInfo();
           //要颗草莓
           Strawberry strawberry = (Strawberry) FruitFactory.createFruit(Strawberry.class.getName());
           strawberry.getInfo();
           //要根香蕉
           Banana banana = (Banana) FruitFactory.createFruit(Banana.class.getName());
           banana.getInfo();
       }
   }
   ```



### 模式总结

优点：实现简单，屏蔽了对象创建过程，实现了对象创建和使用的分离。

缺点：集中了所有产品创建逻辑，职责过重；扩展困难，一旦添加类需修改源码；使用静态工厂方法，工厂角色无法形成继承结构。

使用场景：工厂负责创建对象较少。



## 工厂方法模式

### 模式简介

> 工厂方法模式：定义了一个用于创建对象的接口，但是让子类决定将哪个类实例化。工厂方法让一个类的实例化延迟到了子类。

工厂方法模式在简单工厂模式的基础上，引入了抽象工厂，将具体产品的创建延迟到了具体工厂，克服了简单工厂模式职责过重的缺点。

![](http://image.xiaobailx.top/images/202209111950596.png)

工厂方法模式包括了 4 个角色：

1. 抽象工厂（AbstractFactory）：定义了抽象工厂方法，用于返回一个产品，具体返回哪类产品由具体工厂决定。
2. 抽象产品（AbstractProduct）：是所有具体产品的公共父类，定义了产品的接口。
3. 具体工厂（ConcreteFactory）：是抽象工厂的子类，实现了抽象工厂的工厂方法，返回一个具体产品。
4. 具体产品（ConcreteProduct）：实现了抽象产品接口，由具体工厂创建。



### 应用实例

![image-20220911203219104](http://image.xiaobailx.top/images/202209112032167.png)

1. 抽象工厂——FruitFactory

   ```java
   public abstract class FruitFactory {
       public abstract Fruit createFruit();
   }
   ```
   
2. 抽象产品——Fruit

   ```java
   public abstract class Fruit {
       public abstract void getInfo();
   }
   ```

3. 具体工厂——AppleFactory、BananaFactory、StrawberryFactory

   - AppleFactory

   ```java
   public class AppleFactory extends FruitFactory{
   
       @Override
       public Fruit createFruit() {
           return new Apple();
       }
   }
   ```

   -  BananaFactory

   ```jav
   public class BananaFactory extends FruitFactory{
   
       @Override
       public Fruit createFruit() {
           return new Banana();
       }
   }
   ```

   - StrawberryFactory

   ```java
   public class StrawberryFactory extends FruitFactory{
   
       @Override
       public Fruit createFruit() {
           return new Strawberry();
       }
   }
   ```

3. 具体产品——Apple、Banana、Strawberry

	- Apple

   ```java
   public class Apple extends Fruit {
       @Override
       public void getInfo() {
           System.out.println("这是一个砸中牛顿脑袋的苹果");
       }
   }
   ```

   - Banana

   ```java
   public class Banana extends Fruit {
       @Override
       public void getInfo() {
           System.out.println("这是一根让犹太人致富的香蕉");
       }
   }
   ```

   - Strawberry

   ```java
   public class Strawberry extends Fruit {
       @Override
       public void getInfo() {
           System.out.println("这是一颗甜到你心里的草莓");
       }
   }
   ```

5. 客户端

   ```java
   public class MainTest {
       public static void main(String[] args) throws Exception {
           FruitFactory factory = null;
           //要个苹果
           factory = new AppleFactory();
           Fruit apple = factory.createFruit();
           apple.getInfo();
           //要颗草莓
           factory = new StrawberryFactory();
           Fruit strawberry = factory.createFruit();
           strawberry.getInfo();
           //要根香蕉
           factory = new BananaFactory();
           Fruit banana = factory.createFruit();
           banana.getInfo();
       }
   }
   ```

   

### 模式总结

**优点**

1. 隐藏了对象创建细节，用户创建对象时无需知道具体类名，只需知道相应的工厂类即可创建相应类对象。
2. 在系统中添加新的产品时不会对现有代码产生影响，具有良好的扩展性，符合开闭原则。

**缺点**

1. 每个产品需要添加对应的具体工厂类，类的数量因此成对增加，增加了系统复杂性和性能消耗。
2. 引入了抽象层，增加了系统的抽象性和理解难度。

**使用场景**

1. 客户端不需要知道它所需创建的类对象的类名；
2. 抽象类通过子类来指定创建具体哪个类的对象。



## 抽象工厂模式

### 模式简介

> 抽象工厂：提供一个创建一系列相关或相关以来对象的接口，从而无需指定他们具体的类。

**两个概念**

产品等级结构：即产品的继承结构，水果是个抽象类，其子类包括苹果、香蕉、草莓，则抽象类与具体类之间构成了一个产品等级结构。

产品族：在抽象工厂模式中，产品族是同一个工厂生产的不同产品等级结构中的一组产品。例如当前有两个生产水果的工厂A和B都生产水果苹果、香蕉、草莓，则工厂A生产的苹果、香蕉、草莓构成一个产品族。

![](http://image.xiaobailx.top/images/202209112029984.png)

抽象工厂模式与工厂方法模式的区别在于，工厂方法模式针对的是一个产品等级结构，每一个具体工厂只能创建一个产品；而抽象工厂模式针对的是多个产品等级结构，其每个具体工厂负责创建一族的产品。



抽象工厂模式包括了 4 个角色：

1. 抽象工厂（AbstractFactory）：声明了一组用于创建一族产品的方法，每个方法对应一种产品。
2. 抽象产品（AbstractProduct）：为每种产品声明接口，在抽象产品中声明了产品所具有的业务方法。
3. 具体工厂（ConcreteFactory）：实现了抽象工厂中声明的创建产品的方法，生成了一组具体产品，这些具体产品构成了一个产品族，每个产品位于某个产品等级结构中。
4. 具体产品（ConcreteProduct）：实现了抽象产品接口，由具体工厂创建。



### 应用实例

![image-20220911202602724](http://image.xiaobailx.top/images/202209112026797.png)

1. 抽象工厂——FruitFactory

   ```java
   public abstract class FruitFactory {
   
       public abstract Fruit createApple();
   
       public abstract Fruit createBanana();
   
       public abstract Fruit createStrawberry();
   
   }
   ```

2. 抽象产品

   ```java
   public abstract class Fruit {
       public abstract void getInfo();
   }
   ```

3. 具体工厂——FruitFactoryA、FruitFactoryB

   - FruitFactoryA

   ```java
   public class FruitFactoryA extends FruitFactory {
       
       @Override
       public Fruit createApple() {
           return new AppleA();
       }
   
       @Override
       public Fruit createBanana() {
           return new BananaA();
       }
   
       @Override
       public Fruit createStrawberry() {
           return new StrawberryA();
       }
   }
   ```

   - FruitFactoryB

   ```java
   public class FruitFactoryB extends FruitFactory {
   
       @Override
       public Fruit createApple() {
           return new AppleB();
       }
   
       @Override
       public Fruit createBanana() {
           return new BananaB();
       }
   
       @Override
       public Fruit createStrawberry() {
           return new StrawberryB();
       }
   }
   ```

4. 具体产品AppleA、AppleB、BananaA、BananaB、StrawberryA、StrawberryB

   - AppleA

   ```java
   public class AppleA extends Fruit {
       @Override
       public void getInfo() {
           System.out.println("这是一个砸中牛顿脑袋的苹果，由工厂A生产");
       }
   }
   ```

   - AppleB

   ```java
   public class AppleB extends Fruit {
       @Override
       public void getInfo() {
           System.out.println("这是一个砸中牛顿脑袋的苹果，由工厂B生产");
       }
   }
   ```

   - BananaA

   ```java
   public class BananaA extends Fruit {
       @Override
       public void getInfo() {
           System.out.println("这是一根让犹太人致富的香蕉，由工厂A生产");
       }
   }
   ```

   - BananaB

   ```java
   public class BananaB extends Fruit {
       @Override
       public void getInfo() {
           System.out.println("这是一根让犹太人致富的香蕉，由工厂B生产");
       }
   }
   ```

   - StrawberryA

   ```java
   public class StrawberryA extends Fruit {
       @Override
       public void getInfo() {
           System.out.println("这是一颗甜到你心里的草莓，由工厂A生产");
       }
   }
   ```

   - StrawberryB

   ```java
   public class StrawberryB extends Fruit {
       @Override
       public void getInfo() {
           System.out.println("这是一颗甜到你心里的草莓，由工厂B生产");
       }
   }
   ```

5. 客户端

   ```java
   public class MainTest {
       public static void main(String[] args) throws Exception {
           FruitFactory factoryA = new FruitFactoryA();
           FruitFactory factoryB = new FruitFactoryA();
           //要个苹果
           Fruit appleA = factoryA.createApple();
           appleA.getInfo();
           Fruit appleB = factoryB.createApple();
           appleB.getInfo();
           //要颗草莓
           Fruit strawberryA = factoryA.createStrawberry();
           strawberryA.getInfo();
           Fruit strawberryB = factoryA.createStrawberry();
           strawberryB.getInfo();
           //要根香蕉
           Fruit bananaA = factoryA.createStrawberry();
           bananaA.getInfo();
           Fruit bananaB = factoryA.createStrawberry();
           bananaB.getInfo();
       }
   }
   ```

   

### 模式总结

**优点**

1. 隔离了具体类的生成，更换一个具体工厂就可以在某种程度上改变整个软件系统行为。
2. 当一个产品族的多个对象被设计成一起工作时，能够保证客户始终只是用同一个产品族中的对象。
3. 添加新的产品无需修改已有代码，符合开闭原则。

**缺点**

1. 添加新的产品等级结构需要对原有系统进行较大修改，违背了开闭原则。

**使用场景**

1. 系统有过个产品族，而每次使用只使用其中一个产品族；
2. 产品等级结构稳定，设计完成后不会对系统增加或删除的产品等级结构。



## 工厂模式总结

1. 简单工厂模式隐藏了对象创建细节，将象创建过程与系统业务分离，只需传入响应参数即可获得相应对象，缺点是职责过重。
2. 工厂方法模式引入了抽象工厂，克服了简单工厂模式的缺点，将具体对象创建延迟到了子类且可以灵活扩展，但一定程度上造成了系统的类数量膨胀，损耗系统性能。
3. 抽象工厂模式在工厂方法模式的基础上进一步扩展，每个具体工厂负责创建多个产品等级结构，减小了工厂方法模式造成的类膨胀问题，提供了系统效率。但是，抽象工厂模式存在开闭原则的倾向性，适用于只需扩展产品族，而产品登记结构稳定的系统。