---
title: Java8新特性
date: 2020-12-05
tags:
 - Java
categories:
 - Java基础
---



## Lambda表达式

Lambda 是一个 匿名函数，我们可以把 Lambda 表达式理解为是 一段可以传递的代码（将代码像数据一样进行传递）。使用它可以写出更简洁、更
灵活的代码。



### 语法

1. 语法格式一 ： 无参，无返回值

```java
Runnable r1 = () -> { System.out.println("Hello Lambda"); }
```

2. 语法格式二： Lambda 需要一个参数，但是没有返回值。

```java
Consumer<String> con = (String str) -> { System.out.println(str); }
```

3. 语法格式三 ： 数据类型可以省略 ，因为可由编译器推断得出，称为“类型推断”

```java
Consumer<String> con = (str) -> { System.out.println(str); }
```

4. 语法格式四： Lambda 若只需要一个参数时， **参数的小括号可以省略**

```java
Consumer<String> con = str -> { System.out.println(str); }
```

5. 语法格式五：Lambda 需要两个或以上的参数，多条执行语句，并且可以有返回值

```java
Comparator<Integer> com = (x, y) -> {
    System.out.println("实现函数式接口方法");
    return Integer.compare(x, y);
}
```

6. 语法格式六 ：当 Lambda 体只有 一条语句时，return 与大括号若有都可以省略

```java
Comparator<Integer> com = (x, y) -> return Integer.compare(x, y);
```



## 函数式接口

