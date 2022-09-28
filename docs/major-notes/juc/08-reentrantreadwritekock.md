---
title: 读写锁
date: 2021-12-02
tags:
 - JUC
categories:
 - 高并发编程
---

高并发编程学习笔记，学习资源 ——《尚硅谷高级技术之 JUC 高并发编程》。

本篇笔记包含以下内容：

1. 读写锁介绍，对读写锁的运用场景进行说明，并对其源码进行了简单介绍；
2. 入门案例，通过“使用 ReentrantReadWriteLock 对一个 hashmap 进行读和写操作”案例来说明读写锁的使用。



## 01 读写锁介绍

> 现实中有这样一种场景：对共享资源有读和写的操作，且写操作没有读操作那么频繁。在没有写操作的时候，多个线程同时读一个资源没有任何问题，所以应该允许多个线程同时读取共享资源；但是如果一个线程想去写这些共享资源，就不应该允许其他线程对该资源进行读和写的操作了。
>
> 针对这种场景，**JAVA 的并发包提供了读写锁 ReentrantReadWriteLock，它表示两个锁，一个是读操作相关的锁，称为共享锁；一个是写相关的锁，称为排他锁。**
>
> 1. 线程进入读锁的前提条件：
>    - 没有其他线程的写锁；
>    - 没有写请求, 或者有写请求，但调用线程和持有锁的线程是同一个（可重入锁）。
> 2. 线程进入写锁的前提条件：
>    - 没有其他线程的读锁；
>    - 没有其他线程的写锁。
> 3. 读写锁有以下三个重要的特性：
>    - 公平选择性：支持非公平（默认）和公平的锁获取方式，吞吐量还是非公平优于公平。
>    - 重进入：读锁和写锁都支持线程重进入。
>    - 锁降级：遵循获取写锁、获取读锁再释放写锁的次序，写锁能够降级成为读锁。

**ReentrantReadWriteLock源码：**

```java
public class ReentrantReadWriteLock implements ReadWriteLock, java.io.Serializable {
    private static final long serialVersionUID = -6992448646407690164L;
    
    /** Inner class providing readlock 
     *
     *  读锁 
     */
    private final ReentrantReadWriteLock.ReadLock readerLock;
    
    /** Inner class providing writelock 
     *
     *  写锁 
     */
    private final ReentrantReadWriteLock.WriteLock writerLock;
    
    /** Performs all synchronization mechanics */
    final Sync sync;

    /**
     * Creates a new {@code ReentrantReadWriteLock} with
     * default (nonfair) ordering properties.
     
     * 使用默认（非公平）的排序属性创建一个新的ReentrantReadWriteLock
     */
    public ReentrantReadWriteLock() {
        this(false);
    }

    /**
     * Creates a new {@code ReentrantReadWriteLock} with
     * the given fairness policy.
     *
     * 使用给定的公平策略创建一个新的 ReentrantReadWriteLock 
     */
    public ReentrantReadWriteLock(boolean fair) {
        sync = fair ? new FairSync() : new NonfairSync();
        readerLock = new ReadLock(this);
        writerLock = new WriteLock(this);
    }

    /** 返回用于写入操作的锁 */
    public ReentrantReadWriteLock.WriteLock writeLock() { return writerLock; }
    /** 返回用于读取操作的锁 */
    public ReentrantReadWriteLock.ReadLock  readLock()  { return readerLock; }

    /**
     * Synchronization implementation for ReentrantReadWriteLock.
     * Subclassed into fair and nonfair versions.
     */
    abstract static class Sync extends AbstractQueuedSynchronizer {
        private static final long serialVersionUID = 6317671515068378041L;
        
        static final class NonfairSync extends Sync {}
        
        static final class FairSync extends Sync {}
        
        public static class ReadLock implements Lock, java.io.Serializable {}
        
        public static class WriteLock implements Lock, java.io.Serializable {}
    }
```

>可以看到，ReentrantReadWriteLock 实现了 ReadWriteLock 接口，ReadWriteLock 接口定义了获取读锁和写锁的规范，具体需要实现类去实现；同时其还实现了 Serializable 接口，表示可以进行序列化，在源代码中可以看到 ReentrantReadWriteLock 实现了自己的序列化逻辑。



## 02 入门案例

场景: 使用 ReentrantReadWriteLock 对一个 hashmap 进行读和写操作

```java
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
```



## 小结（重要）

> -  在线程持有读锁的情况下，该线程不能取得写锁(因为获取写锁的时候，如果发现当前的读锁被占用，就马上获取失败，不管读锁是不是被当前线程持有)。
>
> - 在线程持有写锁的情况下，该线程可以继续获取读锁（获取读锁时如果发现写锁被占用，只有写锁没有被当前线程占用的情况才会获取失败）。
>
>   **原因:** 当线程获取读锁的时候，可能有其他线程同时也在持有读锁，因此不能把获取读锁的线程“升级”为写锁；而对于获得写锁的线程，它一定独占了读写锁，因此可以继续让它获取读锁，当它同时获取了写锁和读锁后，还可以先释放写锁继续持有读锁，这样一个写锁就“降级”为了读锁。