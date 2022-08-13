---
title: 三大辅助类
date: 2018-12-02
tags:
 - JUC
categories:
 - 高并发编程
---

> JUC 中提供了三种常用的辅助类，通过这些辅助类可以很好的解决线程数量过多时 Lock 锁的频繁操作。这三种辅助类为：
>
> - CountDownLatch: 减少计数
> - CyclicBarrier: 循环栅栏
> - Semaphore: 信号灯



## 减少计数：CountDownLatch

>CountDownLatch 类可以设置一个计数器，然后通过 countDown() 方法来进行减 1 的操作，使用 await() 方法等待计数器不大于 0，然后继续执行 await() 方法之后的语句。
>
>- CountDownLatch 主要有两个方法，当一个或多个线程调用 await() 方法时，这些线程会阻塞；
>- 其它线程调用 countDown 方法会将计数器减 1（调用 countDown() 方法的线程不会阻塞）；
>- 当计数器的值变为 0 时，因 await() 方法阻塞的线程会被唤醒，继续执行。

```java
/**
 * JUC辅助类1：
 *      减少计数：CountDownLatch
 */

//场景：6个同学陆续离开之后，班长才可以锁门
public class CountDownLatchDemo {
    //6个同学陆续离开之后，班长锁门
    public static void main(String[] args) throws InterruptedException {
        //创建CountDownLatch对象，设置初始值
        CountDownLatch countDownLatch = new CountDownLatch(6);

        //6个同学离开之后
        for (int i = 1; i <= 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+ " 号同学离开教室");
                //计数
                countDownLatch.countDown();
            },String.valueOf(i)).start();
        }
        //等待
        countDownLatch.await();

        System.out.println(Thread.currentThread().getName()+ " 班长锁门离开");
    }
}
```



## 循环栅栏 CyclicBarrier

> CyclicBarrier 看英文单词可以看出大概就是循环阻塞的意思，在使用中 CyclicBarrier 的构造方法第一个参数是目标障碍数，每次执行 CyclicBarrier 一次障碍数会加一，如果达到了目标障碍数，才会执行 cyclicBarrier.await()之后的语句。可以将 CyclicBarrier 理解为加 1 操作。

```java
/**
 * JUC辅助类2：
 *      循环栅栏：CyclicBarrier
 */
//场景：集齐7颗龙珠召唤神龙
public class CyclicBarrierDemo {

    //设置固定值
    private static final int NUMBER = 7;

    public static void main(String[] args) {
        //创建CycliBarrier
        CyclicBarrier cyclicBarrier = new CyclicBarrier(NUMBER,(C)->{
            System.out.println("集齐7颗龙珠召唤神龙");
        });

        //集齐7颗龙珠过程
        for (int i = 1; i <= 7; i++) {
            new Thread(()->{
                try {
                    System.out.println(Thread.currentThread().getName()+ "星龙珠收集到了");
                    //等待
                    cyclicBarrier.await();
                } catch (InterruptedException e){
                    e.printStackTrace();
                } catch (BrokenBarrierException e){
                    e.printStackTrace();
                }
            },String.valueOf(i)).start();
        }
    }
}
```



## 信号灯 Semaphore

> Semaphore 的构造方法中传入的第一个参数是最大信号量（可以看成最大线程池），每个信号量初始化为一个最多只能分发一个许可证。使用 acquire() 方法获得许可证，release() 方法释放许可。

```java
/**
 * JUC辅助类3：
 *      信号灯：Semaphore
 */
//场景3：模拟6辆汽车，3个停车位
public class SemaphoreDemo {
    public static void main(String[] args) {
        //创建Semaphore: 设置许可数量
        Semaphore semaphore = new Semaphore(3);

        //模拟6辆汽车
        for (int i = 1; i <= 6; i++) {
            new Thread(()->{
                try {
                    //抢占
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName()+ " 抢到了车位");
                    //设置随机停车时间
                    TimeUnit.SECONDS.sleep(new Random().nextInt(5));
                    System.out.println(Thread.currentThread().getName()+ " -----离开了停车位");
                } catch (InterruptedException e) {
                    //释放
                    semaphore.release();
                }
            },String.valueOf(i)).start();
        }
    }
}
```



## 总结

![](http://image.xiaobailx.top/images/20211119104551.png)

