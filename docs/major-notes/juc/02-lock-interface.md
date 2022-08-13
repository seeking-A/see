---
title: Lock接口
date: 2021-11-19
tags:
 - JUC
categories:
 - 高并发编程
---

高并发编程学习笔记，学习资源 ——《尚硅谷高级技术之 JUC 高并发编程》。

本篇笔记包含以下内容：

1. Synchronized，对 Synchronized 关键字的使用进行说明；
2. Lock，对 Lock 接口的相关类进行介绍，重点介绍了 ReentrantLock 类和 ReadWriteLock 接口；
3. 实现多线程的多种方式，介绍了创建多线程的 4 种方式。



## 01 synchronized

### synchronized 关键字

>  synchronized 是 Java 中的关键字，是一种同步锁。它修饰的对象有以下几种：
>
> 1. 修饰一个**代码块**，被修饰的代码块称为同步语句块，其作用的范围是大括号 {} 括起来的代码，作用的对象是调用这个代码块的对象；
> 2. 修饰一个**方法**，被修饰的方法称为同步方法，其作用的范围是整个方法，作用的对象是调用这个方法的对象；
> 3. 虽然可以使用 synchronized 来定义方法，但 synchronized 并不属于方法定义的一部分，因此，synchronized 关键字不能被继承。如果在父类中的某个方法使用了 synchronized 关键字，而在子类中覆盖了这个方法，在子类中的这个方法默认情况下并不是同步的，而必须显式地在子类的这个方法中加上synchronized 关键字才可以。当然，还可以在子类方法中调用父类中相应的方法，这样虽然子类中的方法不是同步的，但子类调用了父类的同步方法，因此，子类的方法也就相当于同步了。
> 4. 修饰一个**静态方法**，其作用的范围是整个静态方法，作用的对象是这个类的所有对象；
> 5. 修饰一个**类**，其作用的范围是 synchronized 后面括号括起来的部分，作用的对象是这个类的所有对象。



### 售票案例

```java
class Ticket{
    private int number = 30;
	//操作方法：买票
    public synchronized void sale(){
        if (0<number){
            System.out.println(Thread.currentThread().getName()+ " : 卖出 : " + (number--) + " 剩下: " 		   								+number );
        }
    }
}
```

如果一个代码块被 synchronized 修饰了，当一个线程获取了对应的锁，并执行该代码块时，其他线程便只能一直等待，等待获取锁的线程释放锁，而这里获取锁的线程释放锁只会有两种情况：

1. 获取锁的线程执行完了该代码块，然后线程释放对锁的占有；
2. 线程执行发生异常，此时 JVM 会让线程自动释放锁。那么如果这个获取锁的线程由于要等待 IO 或者其他原因（比如调用 sleep方法）被阻塞了，但是又没有释放锁，其他线程便只能干巴巴地等待，试想一下，这多么影响程序执行效率。因此就需要有一种机制可以不让等待的线程一直无期限地等待下去（比如只等待一定的时间或者能够响应中断），通过 Lock 就可以办到。



### 多线程编程步骤

```java
public class SaleTicket {
    /**
     *  多线程编程步骤
     *      1. 创建资源类，封装属性和方法
     *      2. 创建多线程调用资源类的方法
     *
     *  例: 实现3个售票员卖出30张票
     */

    // 2.创建多个线程调用资源类
    public static void main(String[] args) {
        //创建Ticket对象
        Ticket ticket = new Ticket();
        //创建多个线程
        new Thread(new Runnable() {
            @Override
            public void run() {
                //调用资源
                for (int i = 0; i < 40 ; i++) {
                    ticket.sale();
                }

            }
        },"AA").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                //调用资源
                for (int i = 0; i < 40 ; i++) {
                    ticket.sale();
                }
            }
        },"BB").start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                //调用资源
                for (int i = 0; i < 40 ; i++) {
                    ticket.sale();
                }
            }
        },"CC").start();
    }
}

// 1.创建资源类
class Ticket{
    private int number = 30;

    public synchronized void sale(){
        if (0<number){
            System.out.println(Thread.currentThread().getName()+ " : 卖出 : " + (number--) + " 剩下: " +number );
        }
    }
}
```



## 02 Lock

>  **什么是Lock**
>
> Lock 锁实现提供了比使用同步方法和语句可以获得的更广泛的锁操作。它们允许更灵活的结构，可能具有非常不同的属性，并且可能支持多个关联的条件对象。Lock 提供了比 synchronized 更多的功能

>  **Lock 与的 Synchronized 区别**
>
> - Lock 不是 Java 语言内置的，synchronized 是 Java 语言的关键字，因此是内置特性。Lock 是一个类，通过这个类可以实现同步访问；
> - Lock 和 synchronized 有一点非常大的不同，采用 synchronized 不需要用户去手动释放锁，当 synchronized 方法或者 synchronized 代码块执行完之后，**系统会自动让线程释放对锁的占用**；而 Lock 则必须要用户去**手动释放锁**，如果没有主动释放锁，就有可能导致出现死锁现象。



### Lock接口

```java
public interface Lock {
    //获取锁
    void lock();
    
    void lockInterruptibly() throws InterruptedException;
    
    boolean tryLock();
    
    boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
    //释放锁
    void unlock();
    //返回Condition对象
    Condition newCondition();
}
```



#### lock()

> lock() 方法是平常使用得最多的一个方法，就是用来获取锁。如果锁已被其他线程获取，则进行等待。
>
> 采用 Lock，必须主动去释放锁，并且在发生异常时，不会自动释放锁。因此一般来说，使用 Lock 必须在 try{}catch{}块中进行，并且将释放锁的操作放在 finally 块中进行，以保证锁一定被被释放，防止死锁的发生。通常使用 Lock 来进行同步的话，是以下面这种形式去使用的：

```java
Lock lock = ...;
lock.lock();
try{
	//处理任务
}catch(Exception ex){
    
}finally{
    lock.unlock(); //释放锁
}
```



#### newCondition()

> 关键字 synchronized 与 wait() / notify()这两个方法一起使用可以实现等待/通知模式， Lock 锁的 newContition() 方法返回 Condition 对象，Condition 类也可以实现等待/通知模式。用 notify() 通知时，JVM 会随机唤醒某个等待的线程， 使用 Condition 类可以进行选择性通知，Condition 比较常用的两个方法：
>
> - await() 会使当前线程等待，同时会释放锁,当其他线程调用 signal() 时，线程会重新获得锁并继续执行。
> - signal() 用于唤醒一个等待的线程。
>
> 注意：在调用 Condition 的 await() / signal() 方法前，也需要线程持有相关的 Lock 锁，调用 await() 后线程会释放这个锁，在 singal()调用后会从当前 Condition 对象的等待队列中，唤醒 一个线程，唤醒的线程尝试获得锁，一旦获得锁成功就继续执行。

```java
//第一步 创建资源类，定义属性和操作方法
class Share {
    private int number = 0;

    //创建Lock
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();

    //+1
    public void incr() throws InterruptedException {
        //上锁
        lock.lock();
        try {
            //判断
            while (number != 0) {
                condition.await();
            }
            //干活
            number++;
            System.out.println(Thread.currentThread().getName()+" :: "+number);
            //通知
            condition.signalAll();
        }finally {
            //解锁
            lock.unlock();
        }
    }

    //-1
    public void decr() throws InterruptedException {
        lock.lock();
        try {
            while(number != 1) {
                condition.await();
            }
            number--;
            System.out.println(Thread.currentThread().getName()+" :: "+number);
            condition.signalAll();
        }finally {
            lock.unlock();
        }
    }
}

//测试类
public class ConditionTest {

    public static void main(String[] args) {
        Share share = new Share();
        new Thread(()->{
            for (int i = 1; i <=10; i++) {
                try {
                    share.incr();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"AA").start();
        new Thread(()->{
            for (int i = 1; i <=10; i++) {
                try {
                    share.decr();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"BB").start();

        new Thread(()->{
            for (int i = 1; i <=10; i++) {
                try {
                    share.incr();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"CC").start();   
    }

}
```



### ReentrantLock

> ReentrantLock，意思是“可重入锁”，是唯一实现了 Lock 接口的类，并且 ReentrantLock 提供了更多的方法。下面通过一些实例看具体看一下如何使用:

```java
public class SaleTicket {
    public static void main(String[] args) {
        //创建Ticket对象
        LcTicket ticket = new LcTicket();
        //创建多个线程
        new Thread(()-> {
            //调用资源
            for (int i = 0; i < 40 ; i++) {
                ticket.sale();
            }
        },"AA").start();

        new Thread(()-> {
            //调用资源
            for (int i = 0; i < 40 ; i++) {
                ticket.sale();
            }
        },"BB").start();

        new Thread(()-> {
            //调用资源
            for (int i = 0; i < 40 ; i++) {
                ticket.sale();
            }
        },"CC").start();
    }
}

// 1.创建资源类
class LcTicket{
    private int number = 30;

    public synchronized void sale(){
        ReentrantLock lock = new ReentrantLock();
        //上锁
        lock.lock();
        try {
            if (0<number){
                System.out.println(Thread.currentThread().getName()+ " : 卖出 : " + (number--) + " 剩下: " +number );
            }
        } finally {
            //解锁
            lock.unlock();
        }

    }
}

```



### ReadWriteLock

>  ReadWriteLock 也是一个接口，在它里面只定义了两个方法：

```java
public interface ReadWriteLock {
    /**
     * Returns the lock used for reading.
     *
     * @return the lock used for reading
     */
    Lock readLock();//获取读锁

    /**
     * Returns the lock used for writing.
     *
     * @return the lock used for writing
     */
    Lock writeLock();//获取写锁
}
```

> 一个用来获取读锁，一个用来获取写锁。也就是说将文件的读写操作分开，分成 2 个锁来分配给线程，从而使得多个线程可以同时进行读操作。
>
> 下面的**ReentrantReadWriteLock** 实现了 ReadWriteLock 接口。ReentrantReadWriteLock 里面提供了很多丰富的方法，不过最主要的有两个方法：readLock() 和 writeLock() 用来获取读锁和写锁。
>
> 下面通过几个例子来看一下 ReentrantReadWriteLock 具体用法。

```java
//测试类
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache myCache = new MyCache();
        //创建线程写数据
        for (int i = 1; i <= 5; i++) {
            final int num = i;
            new Thread(()->{
                myCache.put(num+"",num+"");
            },String.valueOf(i)).start();
        }
        //创建线程读数据
        for (int i = 1; i <= 5; i++) {
            final int num = i;
            new Thread(()->{
                myCache.get(num+"");
            },String.valueOf(i)).start();
        }

    }
}

class MyCache {
    //创建map集合
    private volatile Map<String,Object> map = new HashMap<>();

    //创建读写锁对象
    private ReadWriteLock rwLock = new ReentrantReadWriteLock();

    public void put(String key,Object value){
        //添加写锁
        rwLock.writeLock().lock();
        //暂停一会
        try {
            System.out.println(Thread.currentThread().getName()+" 正在写操作"+ key);
            TimeUnit.MICROSECONDS.sleep(300);
            map.put(key,value);
            System.out.println(Thread.currentThread().getName()+" 完成写操作"+ key);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            rwLock.writeLock().unlock();
        }
    }
    public Object get(String key){
        //添加读锁
        rwLock.readLock().lock();
        Object result = null;
        //暂停一会
        try {
            System.out.println(Thread.currentThread().getName()+" 正在读操作"+ key);
            TimeUnit.MICROSECONDS.sleep(300);
            result = map.get(key);
            System.out.println(Thread.currentThread().getName()+" 完成读操作"+ key);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            //释放读锁
            rwLock.readLock().unlock();
        }
        return result;
    }

}
```

> **注意:**
>
> -  如果有一个线程已经占用了读锁，则此时其他线程如果要申请写锁，则申请写锁的线程会一直等待释放读锁。
> -  如果有一个线程已经占用了写锁，则此时其他线程如果申请写锁或者读锁，则申请的线程会一直等待释放写锁。



## 03 实现多线程的多种方式

> 创建多线程的多种方式：
>
> 1. 继承Thread类重写run()方法
> 2. 实现Runable接口中的run()方法
> 3. 实现Callable接口中的call()方法
> 4. 使用线程池方式

```java
//继承Thread类
class MyThread extends Thread{
    public void run(){
        
    }
}

//实现Runnable接口
class MyThread01 implements Runnable{

    @Override
    public void run() {

    }
}

```

通过实现Callable接口创建线程方式详见[Callable&Future接口]()，使用线程池方式创建线程详见[ThreadPool线程池]()



## 总结

![](http://image.xiaobailx.top/images/20211117195553.png)