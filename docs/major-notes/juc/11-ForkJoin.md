---
title: Fork和Join
date: 2021-11-20
tags:
 - JUC
categories:
 - 高并发编程
---

##  01 Fork/Join 框架简介

> Fork/Join 它可以将一个大的任务拆分成多个子任务进行并行处理，最后将子任务结果合并成最后的计算结果，并进行输出。Fork / Join 框架要完成两件事情。
>
> **Fork：把一个复杂任务进行分拆，大事化小。**
>
> **Join ：把分拆任务的结果进行合并。**

![](http://image.xiaobailx.top/images/20211120092359.png)

> **任务分割：** 首先 Fork/Join 框架需要把大的任务分割成足够小的子任务，如果子任务比较大的话还要对子任务进行继续分割。
>
> **执行任务并合并结果：** 分割的子任务分别放到双端队列里，然后几个启动线程分别从双端队列里获取任务执行。子任务执行完的结果都放在另外一个队列里，启动一个线程从队列里取数据，然后合并这些数据。
>
> 在 Java 的 Fork/Join 框架中，使用两个类完成上述操作：
>
> **ForkJoinTask：** 我们要使用 Fork/Join 框架，首先需要创建一个 ForkJoin 任务。该类提供了在任务中执行 fork 和 join 的机制。通常情况下我们不需要直接集成 ForkJoinTask 类，只需要继承它的子类，Fork/Join 框架提供了两个子类：
>
> - RecursiveAction：用于没有返回结果的任务；
> - RecursiveTask:用于有返回结果的任务。
>
> **ForkJoinPool：** ForkJoinTask 需要通过 ForkJoinPool 来执行。
>
> **RecursiveTask：** 继承后可以实现递归(自己调自己)调用的任务。
>
> 
>
> **Fork/Join 框架的实现原理**
>
> ForkJoinPool **由 ForkJoinTask 数组和 ForkJoinWorkerThread 数组组成**，ForkJoinTask 数组负责将存放以及将程序提交给 ForkJoinPool，而ForkJoinWorkerThread 负责执行这些任务。



## 02 fork() 方法

![](http://image.xiaobailx.top/images/20211120094245.png)



![](http://image.xiaobailx.top/images/20211120094134.png)

**fork() 方法的实现原理：** 当我们调用 ForkJoinTask 的 fork 方法时，程序会把任务放在 ForkJoinWorkerThread 的 **workQueue** 中，异步地执行这个任务，然后立即返回结果。源码如下：

```java
public final ForkJoinTask<V> fork() {
    Thread t;
    if ((t = Thread.currentThread()) instanceof ForkJoinWorkerThread)
        ((ForkJoinWorkerThread)t).workQueue.push(this);
    else
        ForkJoinPool.common.externalPush(this);
    return this;
}
```

push(Task) 方法把当前任务存放在 ForkJoinTask 数组队列里。然后再调用 ForkJoinPool 的 signalWork()方法唤醒或创建一个工作线程来执行任务。源码如下：

```java
final void push(ForkJoinTask<?> task) {
    ForkJoinTask<?>[] a; ForkJoinPool p;
    int b = base, s = top, n;
    if ((a = array) != null) {    // ignore if queue removed
        int m = a.length - 1;     // fenced write for task visibility
        U.putOrderedObject(a, ((m & s) << ASHIFT) + ABASE, task);
        U.putOrderedInt(this, QTOP, s + 1);
        if ((n = s - b) <= 1) {
            if ((p = pool) != null)
                p.signalWork(p.workQueues, this);
        }
        else if (n >= m)
            growArray();
    }
}
```



## 03 join() 方法

join() 方法的主要作用是阻塞当前线程并等待获取结果。让我们一起看看 ForkJoinTask 的 join() 方法的实现，源码如下：

```java
public final V join() {
    int s;
    if ((s = doJoin() & DONE_MASK) != NORMAL)
        reportException(s);
    return getRawResult();
}
```

它首先调用 doJoin 方法，通过 doJoin()方法得到当前任务的状态来判断返回什么结果，任务状态有 4 种：

**已完成（NORMAL）、被取消（CANCELLED）、信号（SIGNAL）和出现异常（EXCEPTIONAL）**

- 如果任务状态是已完成，则直接返回任务结果。
- 如果任务状态是被取消，则直接抛出 CancellationException。
- 如果 任务状态是抛出异常，则直接抛出对应的异常。

```java
private int doJoin() {
    int s; Thread t; ForkJoinWorkerThread wt; ForkJoinPool.WorkQueue w;
    //返回执行状态
    return (s = status) < 0 ? s :
    ((t = Thread.currentThread()) instanceof ForkJoinWorkerThread) ?
       //取出任务
        (w = (wt = (ForkJoinWorkerThread)t).workQueue).
        //执行任务
        tryUnpush(this) && (s = doExec()) < 0 ? s :
    wt.pool.awaitJoin(w, this, 0L) :
    externalAwaitDone();
}

final int doExec() {
    int s; boolean completed;
    if ((s = status) >= 0) {
        try {
            completed = exec();
        } catch (Throwable rex) {
            //记录异常
            return setExceptionalCompletion(rex);
        }
        if (completed)
            //执行顺利，设置任务状态为NORMAL
            s = setCompletion(NORMAL);
    }
    return s;
}
```

**在 doJoin()方法流程如下:**

1. 首先通过查看任务的状态，看任务是否已经执行完成，如果执行完成，则直接返回任务状态。
2. 如果没有执行完，则从任务数组里取出任务并执行。
3. 如果任务顺利执行完成，则设置任务状态为 NORMAL，如果出现异常，则记录异常，并将任务状态设置为 EXCEPTIONAL。



## 04 Fork / Join 框架的异常处理

ForkJoinTask 在执行的时候可能会抛出异常，但是我们没办法在主线程里直接捕获异常，所以 ForkJoinTask 提供了 isCompletedAbnormally() 方法来检查任务是否已经抛出异常或已经被取消了，并且可以通过 ForkJoinTask 的 getException() 方法获取异常。

getException 方法返回 Throwable 对象，如果任务被取消了则返回 CancellationException。如果任务没有完成或者没有抛出异常则返回 null。



## 05 入门案例

场景：生成一个计算任务，计算 1+2+3.........+1000，每 100 个数切分一个子任务

```java
class MyTask extends RecursiveTask<Integer> {

    //拆分差值不能超过10，计算10以内运算
    private static final Integer VALUE = 10;
    private int begin;//拆分起始值
    private int end;//拆分末尾值
    private int result;//结束返回值

    //创建有参构造函数
    public MyTask(int begin, int end){
        this.begin = begin;
        this.end = end;
    }

    //拆分合并过程
    @Override
    protected Integer compute() {
        if (end-begin <= VALUE){
            for (int i = begin; i < end; i++) {
                result += i;
            }
        } else {
            //进一步拆分
            int middle = (begin+end)/2;
            //拆分左边
            MyTask task01 = new MyTask(begin,middle);
            //拆分右边
            MyTask task02 = new MyTask(middle+1,end);
            //调用方法拆分
            task01.fork();
            task02.fork();
            //合并结果
            result = task01.join() + task02.join();
        }
        return result;
    }
}

public class ForkJoinDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //创建MyTask对象
        MyTask myTask = new MyTask(0,1000);
        //创建分支合并池对象
        ForkJoinPool forkJoinPool = new ForkJoinPool();
        ForkJoinTask<Integer> forkJoinTask = forkJoinPool.submit(myTask);
        //获取最终合并之后的结果
        try {
            Integer result = forkJoinTask.get();
            System.out.println(result);
        } catch (Exception e){
            e.printStackTrace();
        } finally {
            //关闭池对象
            forkJoinPool.shutdown();
        }
    }
}
```



## 总结

![](http://image.xiaobailx.top/images/20211120103057.png)

