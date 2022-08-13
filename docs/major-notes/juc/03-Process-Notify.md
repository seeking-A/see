---
title: 进程通信
date: 2021-11-26
tags:
 - JUC
categories:
 - 高并发编程
---

高并发编程学习笔记，学习资源 ——《尚硅谷高级技术之 JUC 高并发编程》。

本篇笔记包含以下内容：

1. BlockingQueue 简介，从 BlockingQueue 的数据结构和运用场景对其进行简单介绍；
2. 核心方法，对 BlockingQueue 的增、删、查操作等主要方法进行说明并演示；
3. 常见类型，分别对 ArrayListBlockingQueue 、LinkedBlockingQueue、SynchronousQueue 等常见阻塞队列进行介绍。



线程间通信的模型有两种：**共享内存和消息传递**，以下方式都是基本这两种模型来实现的。我们来基本一道面试常见的题目来分析。

#### 01 synchronized 方案

```java
/**
 *  synchronized 关键字实现线程交替加减
 */
public class TestSynchronized  {
    /**
     * 交替加减
     * @param args
     */
    public static void main(String[] args){
        DemoClass demoClass = new DemoClass();
        new Thread(() ->{
            for (int i = 0; i < 5; i++) {
                demoClass.increment();
            }
        }, "线程 A").start();
        new Thread(() ->{
            for (int i = 0; i < 5; i++) {
                demoClass.decrement();
            }
        }, "线程 B").start();
    }
}


class DemoClass{
    //加减对象
    private int number = 0;
    /**
     * 加 1
     */
    public synchronized void increment() {
        try {
            while (number != 0){
                this.wait();
            }
            number++;
            System.out.println("--------" + Thread.currentThread().getName() + "加一成功---,值为:" + number);
            notifyAll();
        }catch (Exception e){
            e.printStackTrace();
        }
    }
    /**
     * 减一
     */
    public synchronized void decrement(){
        try {
            while (number == 0){
                this.wait();
            }
            number--;
            System.out.println("--------" + Thread.currentThread().getName() + "减一成功---,值为:" + number);
            notifyAll();
        }catch (Exception e){
            e.printStackTrace();
        }
    }
}

```

> Synchronized实现同步的基础：Java中每一个对象都可以作为锁。
>
> 具体表现为以下3种形式：
>
> - 对于普通方法，锁是当前实例对象；
> - 对于静态方法，锁是当前类的Class对象；
> - 对于同步方法块，锁是Synchronized括号里配置的对象。



#### 02 Lock 方案

```java
/**
 *  使用Lock实现线程交替加减
 */
public class TestLock  {
    /**
     * 交替加减
     * @param args
     */
    public static void main(String[] args){
        DemoClass1 demoClass = new DemoClass1();
        new Thread(() ->{
            for (int i = 0; i < 5; i++) {
                demoClass.increment();
            }
        }, "线程 A").start();
        new Thread(() ->{
            for (int i = 0; i < 5; i++) {
                demoClass.decrement();
            }
        }, "线程 B").start();
    }
}


class DemoClass1 {
    //加减对象
    private int number = 0;
    //声明锁
    private Lock lock = new ReentrantLock();
    //声明钥匙
    private Condition condition = lock.newCondition();
    /**
     * 加 1
     */
    public void increment() {
        try {
            lock.lock();
            while (number != 0){
                condition.await();
            }
            number++;
            System.out.println("--------" + Thread.currentThread().getName() + "加一成功----------,值为:" + number);
            condition.signalAll();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
    /**
     * 减一
     */
    public void decrement(){
        try {
            lock.lock();
            while (number == 0){
                condition.await();
            }
            number--;
            System.out.println("--------" + Thread.currentThread().getName() + "减一成功----------,值为:" + number);
            condition.signalAll();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}
```



#### 03 线程间定制化通信

问题: A 线程打印 5 次 A，B 线程打印 10 次 B，C 线程打印 15 次 C,按照此顺序循环 10 轮

```java
//第一步 创建资源类
class ShareResource {
    //定义标志位
    private int flag = 1;  // 1 AA     2 BB     3 CC

    //创建Lock锁
    private Lock lock = new ReentrantLock();

    //创建三个condition
    private Condition c1 = lock.newCondition();
    private Condition c2 = lock.newCondition();
    private Condition c3 = lock.newCondition();

    //打印5次，参数第几轮
    public void print5(int loop) throws InterruptedException {
        //上锁
        lock.lock();
        try {
            //判断
            while(flag != 1) {
                //等待
                c1.await();
            }
            //干活
            for (int i = 1; i <=5; i++) {
                System.out.println(Thread.currentThread().getName()+" :: "+i+" ：轮数："+loop);
            }
            //通知
            flag = 2; //修改标志位 2
            c2.signal(); //通知BB线程
        }finally {
            //释放锁
            lock.unlock();
        }
    }

    //打印10次，参数第几轮
    public void print10(int loop) throws InterruptedException {
        lock.lock();
        try {
            while(flag != 2) {
                c2.await();
            }
            for (int i = 1; i <=10; i++) {
                System.out.println(Thread.currentThread().getName()+" :: "+i+" ：轮数："+loop);
            }
            //修改标志位
            flag = 3;
            //通知CC线程
            c3.signal();
        }finally {
            lock.unlock();
        }
    }

    //打印15次，参数第几轮
    public void print15(int loop) throws InterruptedException {
        lock.lock();
        try {
            while(flag != 3) {
                c3.await();
            }
            for (int i = 1; i <=15; i++) {
                System.out.println(Thread.currentThread().getName()+" :: "+i+" ：轮数："+loop);
            }
            //修改标志位
            flag = 1;
            //通知AA线程
            c1.signal();
        }finally {
            lock.unlock();
        }
    }
}

public class ReentrantLockTest {
    public static void main(String[] args) {
        ShareResource shareResource = new ShareResource();
        new Thread(()->{
            for (int i = 1; i <=10; i++) {
                try {
                    shareResource.print5(i);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"AA").start();

        new Thread(()->{
            for (int i = 1; i <=10; i++) {
                try {
                    shareResource.print10(i);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"BB").start();

        new Thread(()->{
            for (int i = 1; i <=10; i++) {
                try {
                    shareResource.print15(i);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"CC").start();
    }
}
```

