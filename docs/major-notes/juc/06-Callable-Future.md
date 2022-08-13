---
title: Callable接口
date: 2021-11-19
tags:
 - JUC
categories:
 - 高并发编程
---

#### Callable 接口

> Callable 接口的特点如下(重点)
>
> - 为了实现 Runnable，需要实现不返回任何内容的 run() 方法，而对于 Callable，需要实现在完成时返回结果的 call() 方法。
> - call() 方法可以引发异常，而 run() 则不能。
> - 为实现 Callable 而必须重写 call() 方法。
> - 不能直接替换 Runnable，因为 Thread 类的构造方法根本没有 Callable。
>
> 
>
> Runable接口和Callable接口创建线程比较
>
>  * 是否有返回值 Callable接口有返回值，Runable 接口无返回值。
>  * 是否触发异常 Callable接口会抛出异常，Runable 接口不抛出异常。
>  * 实现方法名称不同，Callable接口实现call()方法，Runable 接口实现 run() 方法。
>
> 
>
> 找一个类，即和 Runable 有关系，又和 Callable 有关系。
>
>  * Runable 接口有实现类 FutureTask。
>  *   FutureTask 构造可以传递 Callable。

```java
//创建新类MyThread实现runnable接口
class MyThread implements Runnable{
    @Override
    public void run() {
        
    }
}

//新类MyThread2实现callable接口
class MyThread2 implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
    	return 200;
    }
}
```



#### Future 接口

> 当 call() 方法完成时，结果必须存储在主线程已知的对象中，以便主线程可以知道该线程返回的结果。为此，可以使用 Future 对象。将 Future 视为保存结果的对象–它可能暂时不保存结果，但将来会保存（一旦 Callable 返回）。Future 基本上是主线程可以跟踪进度以及其他线程的结果的一种方式。要实现此接口，必须重写 5 种方法，这里列出了重要的方法,如下:
>
> - **public boolean cancel (boolean mayInterrupt)：用于停止任务。**
>
>   如果尚未启动，它将停止任务。如果已启动，则仅在 mayInterrupt 为 true时才会中断任务。
>
> - **public Object get() 抛出 InterruptedException**
>
>   ExecutionException：用于获取任务的结果。  如果任务完成，它将立即返回结果，否则将等待任务完成，然后返回结果。
>
> - **public boolean isDone()：如果任务完成，则返回 true，否则返回 false**
>
>   可以看到 Callable 和 Future 做两件事—— Callable 与 Runnable 类似，因为它封装了要在另一个线程上运行的任务，而 Future 用于存储从另一个线程获得的结果。实际上，Future 也可以与 Runnable 一起使用。
>
>   要创建线程，需要 Runnable。为了获得结果，需要 Future。



#### FutureTask

> Java 库有具体的 FutureTask 类型，该类型实现了 Runnable 和 Future，并方便地将两种功能组合在一起。 可以通过为其构造函数提供 Callable 来创建 FutureTask。然后，将 FutureTask 对象提供给 Thread 的构造函数以创建 Thread 对象。因此，间接地使用 Callable 创建线程。
>
> **核心原理:(重点)**
> 在主线程中需要执行比较耗时的操作时，但又不想阻塞主线程时，可以把这些作业交给 Future 对象在后台完成。
>
> - 当主线程将来需要时，就可以通过 Future 对象获得后台作业的计算结果或者执行状态。
> - 一般 FutureTask 多用于耗时的计算，主线程可以在完成自己的任务后，再去获取结果。
> - 仅在计算完成时才能检索结果；如果计算尚未完成，则阻塞 get() 方法。
> - 一旦计算完成，就不能再重新开始或取消计算。
> - get() 方法而获取结果只有在计算完成时获取，否则会一直阻塞直到任务转入完成状态，然后会返回结果或者抛出异常。
> - get() 只计算一次，因此 get() 方法放到最后。



#### 使用 Callable 和 和 Future

```java
/**
 * 
 * 找一个类，即和Runable有关系，又和Callable有关系
 *
 *   Runable接口有实现类FutureTask
 *
 *   FutureTask构造可以传递Callable
 * 
 */


//实现Runnable接口
class MyThread01 implements Runnable{

    @Override
    public void run() {

    }
}

//实现Callable接口
class MyThread02 implements Callable {

    @Override
    public Integer call() throws Exception{
        System.out.println(Thread.currentThread().getName()+" come in callable");
        return 200;
    }
}

public class Demo01 {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //Runnable接口创建线程
        new Thread(new MyThread01(),"AA").start();

        //Callable接口创建线程 报错
        //new Thread(new MyThread02()).start();

        //为构造函数提供 Callable 来创建FutureTask
        FutureTask<Integer> futureTask1 = new FutureTask<>(new MyThread02());

        //lambda表达式
        FutureTask<Integer> futureTask2 = new FutureTask<>(()->{
            System.out.println(Thread.currentThread().getName()+" come in callable");
            return 1024;
        });

        //创建一个线程
        new Thread(futureTask1,"mary").start();
        new Thread(futureTask2,"lucy").start();

        /*while (!futureTask2.isDone()){
            System.out.println("wait...");
        }*/

        //调用FutureTask的get方法
        System.out.println(futureTask1.get());
        System.out.println(futureTask2.get());

        System.out.println(Thread.currentThread().getName()+ " come over");

        //FutureTask原理
        /**
         * 1. 老师上课，口渴了，买票不合适，讲课线程继续，单开线程找班长去买水，把水买回来后，需要时直接get
         * 2. 4个同学，1同学1+2...+5 , 2同学 10+11+12...+50 3同学 60+61+62 , 4同学 100+200
         *      第2个同学计算量较大
         *      FutureTask单开线程给2同学计算，先汇总1 3 4，最后等2同学计算完成后，统一汇总
         * 3. 考试，先做会的题目，最后看不会做的题目
         */
    }
}

```



#### 总结

![image-20211118165012208](http://image.xiaobailx.top/images/20211118165012.png)
