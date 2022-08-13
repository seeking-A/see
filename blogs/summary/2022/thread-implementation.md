---
title: 常见的多线程实现方式
date: 2022-07-02
tags:
 - 多线程
categories:
 - 复盘
---

多线程在面试过程中是必考问题，最近写项目时用到了多线程，所以在此做个总结。

常见的多线程创建方式有四种：

1. 继承 Thread 类重写 run 方法
2. 实现 Runnable 接口的 run 方法
3. 实现 Callable 接口的 call 方法，然后使用 FutureTask 进行封装
4. 使用线程池的方式实现

## 1. 继承 Thread 类实现

```java
public class ThreadDemo{
    public static void main(String[] args) {
        for (int i = 0; i < 5; i++) {
            //创建并启动线程
            new MyThread().start();
        }
    }
}

class MyThread extends Thread{
    //重写run方法
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"由继承Thread创建");
    }
}
```



## 2. 实现 Runnable 接口

```java
public class ThreadDemo{
    public static void main(String[] args) {
        for (int i = 0; i < 5; i++) {
            //创建并启动线程
            new Thread(new Runner(),"Thread"+i).start();
        }
    }
}

class Runner implements Runnable{
    //实现run方法
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"有实现Runnable接口创建");
    }
}
```



## 3. 实现 Callable 接口

```java
public class ThreadDemo{
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        for (int i = 1; i <= 5; i++) {
            //使用FutureTask保存线程结果
            FutureTask<String> futureTask = new FutureTask<String>(new Caller());
            //创建并启动线程
            new Thread(futureTask,"Thread"+i).start();
            //获取线程执行结果
            System.out.println(futureTask.get());
        }
    }
}

class Caller implements Callable{
    //实现Call接口
    @Override
    public Object call() throws Exception {
        String result = Thread.currentThread().getName()+"由实现Callable接口创建";
        return result;
    }
}
```



## 4. 线程池方式

```java
public class PoolDemo{
    public static void main(String[] args) {
        /**
         * 线程池核心参数
         * 1.corePoolSize: 核心线程数
         * 2.maximumPoolSize: 最大线程数
         * 3.keepAliveTime: 线程存活时间
         * 4.unit: 时间单位
         * 5.workQueue: 阻塞队列
         * 6.threadFactory: 线程工厂
         * 7.AbortPolicy: 拒绝策略
         **/

        //1.自定义线程池
        ExecutorService executor = new ThreadPoolExecutor(
                3,
                3,
                1L,
                TimeUnit.MILLISECONDS,
                new ArrayBlockingQueue<>(5),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()
        );

        //2.提交任务
        for (int i = 0; i < 5; i++) {
            executor.execute(()->{
                System.out.println(Thread.currentThread().getName()+"由线程池创建");
            });
        }
    }
}
```



关于以上几种实现方式的对比和区别，可以参考我另外两篇文章：

[使用Thread类和Runnable接口实现多线程的区别]()

[使用Runnable接口和Callable接口实现多线程的区别]()

线程池这块内容还挺多的，可以参考一下我的这篇文章：

[使用线程池实现多线程]()

