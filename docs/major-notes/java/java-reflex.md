---
title: Java反射机制
date: 2020-11-01
tags:
 - Java
categories:
 - Java基础
---

## 什么是 Java 反射

简答的说就是通过 Class 对象知道一个类的结构信息。

当一个类加载完之后在堆内存中产生一个 Class 类型对象，该对象包含了完整的类结构信息。我们通过 Class 对象看到了类结构，类似于通过镜子看到一个类的结构，形象称之为反射。



## 提供的功能

- 在运行时判断任意一个对象所属的类；
- 在运行时构造任意一个类的对象；
- 在运行时判断任意一个类所具有的成员变量和方法；
- 在运行时获取泛型信息；
- 在运行时调用任意一个对象的成员变量和方法；
- 在运行时处理注解；
- 生成动态代理。



## 主要的 API

- java.lang.Class:代表一个类
- java.lang.reflect.Method:代表类的方法
- java.lang.reflect.Field:代表类的成员变量
- java.lang.reflect.Constructor:代表类的构造器



## Class 类及实例获取

public final Class getClass()：返回一个Class 类对象，定义在 Object 中，被所有子类继承。Class 类是 Java 反射的源头。

### 主要方法

| 方法名                                           | 功能说明                                                     |
| ------------------------------------------------ | ------------------------------------------------------------ |
| static Class forName(String name)                | 返回指定类名 name 的 Class 对象                              |
| Object newInstance()                             | 调用缺省构造函数，返回该Class对象的一个实例                  |
| getName()                                        | 返回此Class对象所表示的实体（类、接口、数组类、基本类型或void）名称 |
| Class getSuperClass()                            | 返回当前Class对象的父类的Class对象                           |
| Class [] getInterfaces()                         | 获取当前Class对象的接口                                      |
| ClassLoader getClassLoader()                     | 返回该类的类加载器                                           |
| Class getSuperclass()                            | 返回表示此Class所表示的实体的超类的Class                     |
| Constructor[] getConstructors()                  | 返回一个包含某些Constructor对象的数组                        |
| Field[] getDeclaredFields()                      | 返回Field对象的一个数组                                      |
| Method getMethod(String name,Class … paramTypes) | 返回一个Method对象，此对象的形参类型为paramType              |



###  Class 实例获取

1. 已知具体的类，通过类的class属性获取，该方法最为安全可靠，程序性能最高。

   ```java
   Class clazz = String.class;
   ```

2. 已知某个类的实例，调用该实例的 getClass() 方法获取Class对象。

   ```java
   Person p1=new Person();
   Class clazz2=p1.getClass();
   ```

3. 已知一个类的全类名，且该类在类路径下，可通过Class类的静态方法 forName() 获取，可能抛出 ClassNotFoundException。

   ```java
   Class clazz = Class.forName(“java.lang.String”);
   ```

4. 其他方式(不做要求)。

   ```java
   ClassLoader cl = this.getClass().getClassLoader();
   Class clazz4 = cl.loadClass(“类的全类名”);
   ```



##  类的加载与 ClassLoader

### 类的加载过程

加载 --> 链接 --> 初始化

- 加载：字节码文件加载到内存中，将静态数据转为方法区的运行时数据，生成一个代表该类的 Class 对象。
- 链接：将二进制代码合并到 JVM 运行状态中的过程。分为验证、准备和解析三个步骤。
  - 验证：确保类信息符合  JVM 规范；
  - 准备：为类变量分配内存并设置 **类默认初始值** 的阶段，内存在 **方法区** 进行；
  - 解析：将常量池内的符号引用（常量名）替换为直接引用（地址）的过程。
- 初始化：执行 **类构造器 ```<clinit>()```** 方法过程。
  - 类构造器 ```<clinit>()```方法是由编译期自动收集类中所有类变量的赋值动作和静态代码块中的语句合并产生的；
  - 当一个类初始化时，其父类还没有进行初始化，则需要先触发其父类的初始化；
  - 虚拟机会保证一个类的 ```<clinit>()```方法在多线程环境中被正确加锁和同步。

### 类加载器

作用：用来把类(class)装载进内存中。

类型：引导类加载器 (Bootstrap ClassLoader)、扩展类加载器 (Extension Class Loader)、系统类加载器 (AppClassLoader)

- **Bootstrap ClassLoader :** 由 C/C++ 语言实现，打包加载 Java 的核心库，用于提供 JVM 自身需要的类，作为加载扩展类和应用程序类加载器的父类。

- **Extension Class Loader :** Java 语言编写，父类加载器为启动类加载器，从java.ext.dirs系统属性所指定的目录中加载类库，或从JDK的安装目录的jre/lib/ext子目录（扩展目录）下加载类库。

- **AppClassLoader :** Java 语言编写，父类加载器是扩展类加载器，负责加载环境变量类路径或系统属性java.class.path指定路径下的类库。

```java
public void ClassLoaderTest() throws Exception{
    //对于自定义类，使用系统类加载器进行加载
    ClassLoader classLoader1=Demo03_ClassLoader.class.getClassLoader();
    System.out.println(classLoader1);
    //调用系统类加载器的getParent():获取扩展类加载器
    ClassLoader classLoader2=classLoader1.getParent();
    System.out.println(classLoader2);
    //调用扩展类加载器的getParent():无法获取引导类加载器
    //引导类加载器主要负责加载器java的核心库，无法加载自定义类的
    ClassLoader classLoader3=classLoader2.getParent();
    System.out.println(classLoader3);

    ClassLoader classLoader4=String.class.getClassLoader();
    System.out.println(classLoader4);
}
```



### 获取类的完整结构

```java
通过反射获取运行时类的完整结构
     Field、Method、Constructor、Superclass、Interface、Annotation
使用反射获取：
     1.实现的全部接口
         public Class<?>[] getInterfaces()
     2.所继承的父类
         public Class<? Super T> getSuperclass()
     3.全部的构造器
         public Constructor<T>[] getConstructors()
         public Constructor<T>[] getDeclaredConstructors()
         Constructor类中：
             取得修饰符: public int getModifiers();
             取得方法名称: public String getName();
             取得参数的类型：public Class<?>[] getParameterTypes();
     4.全部的方法
         public Method[] getDeclaredMethods()
         public Method[] getMethods()
         Method类中：
             public Class<?> getReturnType()取得全部的返回值
             public Class<?>[] getParameterTypes()取得全部的参数
             public int getModifiers()取得修饰符
             public Class<?>[] getExceptionTypes()取得异常信息
      5.全部的Field
         public Field[] getFields()
         public Field[] getDeclaredFields()
         Field方法中：
             public int getModifiers() 以整数形式返回此Field的修饰符
             public Class<?> getType() 得到Field的属性类型
             public String getName() 返回Field的名称。
       6. Annotation相关
             get Annotation(Class<T> annotationClass)
             getDeclaredAnnotations()
       7.泛型相关
             获取父类泛型类型：Type getGenericSuperclass()
             泛型类型：ParameterizedType
             获取实际的泛型类型参数数组：getActualTypeArguments()
       8.类所在的包
             Package getPackage()
```



### 调用类的指定结构

#### 调用指定方法

1. 通过Class类的getMethod(String name,Class…parameterTypes)方法取得一个Method对象，并设置此方法操作时所需要的参数类型。

2. 之后使用Object invoke(Object obj, Object[] args)进行调用，并向方法中传递要设置的obj对象的参数信息。Object invoke(Object obj, Object[] args) 说明：

   - Object对应原方法的返回值，若原方法无返回值，此时返回 null 原方法若为静态方法，此时形参Object obj可为null

   - 若原方法形参列表为空，则Object[] args为null

   - 原方法声明为private,则需要在调用此invoke()方法前，显式调用方法对象的setAccessible(true)方法，将可访问private的方法。



####  setAccessible( )

- Method和Field、Constructor对象都有setAccessible()方法。
- setAccessible 启动和禁用访问安全检查的开关。
- 参数值为 true 则指示反射的对象在使用时应该取消 Java 语言访问检查。
  - 提高反射的效率。如果代码中必须用反射，而该句代码需要频繁的被调用，那么请设置为true;
  - 使得原本无法访问的私有成员也可以访问。
- 参数值为false则指示反射的对象应该实施Java语言访问检查。

```java
@Test
public void InvokeMethodTest() throws Exception {
    //创建运行时类的对象
    Class<Person> clazz=Person.class;
    Person person=clazz.newInstance();
    //获取指定的某个方法
    //方式一：调用getMethod()，获取运行时类指定的公共方法
    Method setAge=clazz.getMethod("setAge", int.class);
    setAge.invoke(person,18);
    //方式二：调用getDeclaredMthod()，获取运行时类所有方法
    //访问公共方法
    Method setName=clazz.getDeclaredMethod("setName",String.class);
    setName.invoke(person,"Tom");
    Method showInfo=clazz.getDeclaredMethod("showInfo", String.class, Person.class);
    //通过调用setAccessible()方法保证当前方法是可访问的
    showInfo.setAccessible(true);
    //调用方法invoke()，返回值即为对应类中调用的返回方法的返回值
    showInfo.invoke(person,"中国",new Person("小爱",18));
    System.out.println(person);
}
```



#### 调用指定属性

在反射机制中，可以直接通过Field类操作类中的属性，通过Field类提供的set()和get()方法就可以完成设置和取得属性内容的操作。

>  public Field getField(String name) 返回此Class对象表示的类或接口的指定的public的Field。
>  public Field getDeclaredField(String name)返回此Class对象表示的类或接口的指定的Field。
>
> 在Field中：
>         public Object get(Object obj) 取得指定对象obj上此Field的属性内容
>         public void set(Object obj,Object value) 设置指定对象obj上此Field的属性内容

```java
    @Test
    public void InvokeField() throws Exception {
        Class<Person> clazz=Person.class;
        Field nameField = clazz.getDeclaredField("name");
        //调用newInstance()方法创建一个实例对象
        Person person=clazz.newInstance();
        //保证属性可以被安全访问
        nameField.setAccessible(true);
        //调用set()方法为实例的属性赋值
        nameField.set(person,"Tom");
        Field ageField=clazz.getField("age");
        ageField.set(person,18);

        String name= (String) nameField.get(person);
        int age = (int) ageField.get(person);

        System.out.println("name:"+name+"   age:"+age);
    }
}
```



##  动态代理

动态代理相比于静态代理的优点：抽象角色中（接口）声明的所有方法都被转移到调用处理器一个集中的方法中处理，可以更加灵活和统一地处理众多的方法。

### Java动态代理相关API

​     Proxy ：专门完成代理的操作类，是所有动态代理类的父类。通过此类为一个或多个接口动态地生成实现类。
​     提供用于创建动态代理类和动态代理对象的静态方法
​         ```static Class<?> getProxyClass(ClassLoader loader, Class<?>... interfaces)```
​             创建一个动态代理类所对应的Class对象
​         ```static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)```
​             直接创建一个动态代理对象



### 动态代理实现步骤

     1. 创建一个实现接口InvocationHandler的类，它必须实现invoke方法，以完成代理的具体操作；
     2. 创建被代理的类以及接口；
     3. 通过Proxy的静态方法 ```newProxyInstance(ClassLoader loader, Class[] interfaces, InvocationHandler h)``` 创建一个Subject接口代理；
     4. 通过 Subject 代理调用RealSubject实现类的方法



### 动态代理与 AOP

```java

/**
 * 1. 创建一个实现接口InvocationHandler的类，它必须实现invoke方法，以完成代理的具体操作
 **/
class MyInvocationHandler implements InvocationHandler{

    private Object obj;//需要使用代理类的对象进行赋值

    public void bind(Object obj){
        this.obj=obj;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        HumanUtil util=new HumanUtil();
        util.method1();

        //method:即代理类对象调用的方法，此方法也就作为了被代理对象要调用的方法
        //obj：被代理对象
        Object returnValue=method.invoke(obj,args);
        util.method2();
        //上述方法的返回值就作为类中invoke()的返回值
        return returnValue;
    }
}

/**
 * 2. 创建被代理的类以及接口
 **/
interface Human{
    String getBelieve();

    void eat(String food);
}

//接口实现类
class SuperMan implements Human{

    @Override
    public String getBelieve() {
        return "I believe I can fly!";
    }

    @Override
    public void eat(String food) {
        System.out.println("我喜欢吃"+food);
    }
}

//通用方法
class HumanUtil{

    public void method1(){
        System.out.println("====================通用方法1====================");
    }

    public void method2(){
        System.out.println("====================通用方法2====================");
    }
}

/**
 * 3.通过Proxy的静态方法newProxyInstance(ClassLoader loader, Class[] interfaces, InvocationHandler h)
 * 创建一个Subject接口代理
 **/
class ProxyFactory{
    public static Object getProxyInstance(Object obj){
        MyInvocationHandler handler=new MyInvocationHandler();
        handler.bind(obj);
        return Proxy.newProxyInstance(obj.getClass().getClassLoader(),obj.getClass().getInterfaces(),handler);
    }
}

/**
 * 4.通过Subject代理调用RealSubject实现类的方法
 **/
public class Demo07_Proxy {
    
    public static void main(String[] args) {
        SuperMan superMan=new SuperMan();
        //proxyInstace：代理类对象
        Human proxyInstance=(Human)ProxyFactory.getProxyInstance(superMan);
        //当通过代理对象调用方法时，会自动调用被代理类中同名的方法
        String believe=proxyInstance.getBelieve();
        System.out.println(believe);

        proxyInstance.eat("四川麻辣烫");

    }
}

```

