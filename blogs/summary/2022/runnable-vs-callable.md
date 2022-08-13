---
title: 使用Runnable和Callable接口实现多线程的区别
date: 2022-07-02
tags:
 - 多线程
categories:
 - 复盘
---

先看两种实现方式的步骤：

**1.实现Runnable接口**

```java
public class ThreadDemo{
    public static void main(String[] args) {
        for (int i = 1; i <= 5; i++) {
             //创建并启动由实现Runnable接口创建的线程
            new Thread(new Runner(),"Thread"+i).start();
        }
    }
}

//实现Runnable接口
class Runner implements Runnable{
    //实现run方法
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+"有实现Runnable接口创建");
    }
}
```

**2.实现 Callable 接口**

```java
public class ThreadDemo{
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        for (int i = 1; i <= 5; i++) {
            //使用FutureTask保存线程结果
            FutureTask<String> futureTask = new FutureTask<String>(new Caller());
            //创建并启动由实现Callable创建的线程
            new Thread(futureTask,"Thread"+i).start();
            //获取线程执行结果
            System.out.println(futureTask.get());
        }
    }
}

//实现Callable接口
class Caller implements Callable{
    //实现Call接口
    @Override
    public Object call() throws Exception {
        String result = Thread.currentThread().getName()+"由实现Callable接口创建";
        return result;
    }
}
```

从以上代码可以看出，使用 Callable 接口创建多线程时使用了 FutureTask 进行封装，然后 FutureTask 作为参数传给 Thread 的构造函数，而 FutureTask 的作用是存放 Callable 接口 call 方法的返回值。我们来看一下 FutureTask 的源码

```java
//FutureTask实现了RunnableFuture接口
public class FutureTask<V> implements RunnableFuture<V> {}

//再看RunnableFuture接口，继承了Runnable接口
public interface RunnableFuture<V> extends Runnable, Future<V> {
    void run();
}

//回到FutureTask,找到run方法
public void run() {
    ...
    Callable<V> c = callable;
    if (c != null && state == NEW) {
        V result;
        boolean ran;
        try {
            //获取call的返回值
            result = c.call();
            ran = true;
        } catch (Throwable ex) {
            result = null;
            ran = false;
            setException(ex);
        }
        if (ran)
            set(result);
    }
    ...
}

//看一下set方法
protected void set(V v) {
    if (UNSAFE.compareAndSwapInt(this, stateOffset, NEW, COMPLETING)) {
        outcome = v;
        ...
    }
}

//再看一下FutureTask的构造函数
public FutureTask(Callable<V> callable) {
        if (callable == null)
            throw new NullPointerException();
        this.callable = callable;
        this.state = NEW;
}
```

从对 FutureTask 的源码分析，我们可以看出 FutureTask 实现了 Runnable 接口的 run 方法，在 run 方法中调用了 Callable 的 call 方法，将结果保存到 result 中，通过 set 方法将结果存储至 outcome 变量中。

通过以上分析，我们总结出使用 Runnable 和 Callable 接口的区别：

1. 使用 Runnable 接口实现更加简单，而 通过 Callable 接口创建线程需要 FutureTask 进行封装；
2. 通过实现 Runnable 接口创建的线程没有返回值，而使用 Callable 接口创建的线程可以有返回结果，并保存在 FutureTask中；
3. 通过实现 Runnable 接口创建线程不抛出异常，而使用 Callable 接口创建的线程会抛出异常；

从以上总结可以看出，我们也可以看出 Runnable 适用于无需返回值的场景，而 Callable 接口用于需要保存返回值的场景。

