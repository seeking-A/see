---
title: 多线程锁
date: 2021-11-27
tags:
 - JUC
categories:
 - 高并发编程
---

高并发编程学习笔记，学习资源 ——《尚硅谷高级技术之 JUC 高并发编程》。

本篇笔记通过引出**八锁问题**，从而对多线程锁的运用原理进行解释说明。



#### 锁的八个问题（八锁问题）

```java
class Phone {
    public static synchronized void sendSMS() throws Exception {
        // 停留 4 秒
        TimeUnit.SECONDS.sleep(4);
        System.out.println("------sendSMS");
    }

    public synchronized void sendEmail() throws Exception {
        System.out.println("------sendEmail");
    }

    public void getHello() {
        System.out.println("------getHello");
    }
}


/**
 * @Description: 8锁
 * 
 * 1 标准访问，先打印短信还是邮件
 * ------sendSMS
 * ------sendEmail
 *
 * 2 停4秒在短信方法内，先打印短信还是邮件
 * ------sendSMS
 * ------sendEmail
 *
 * 3 新增普通的hello方法，是先打短信还是hello
 * ------getHello
 * ------sendSMS
 *
 * 4 现在有两部手机，先打印短信还是邮件
 * ------sendEmail
 * ------sendSMS
 *
 * 5 两个静态同步方法，1部手机，先打印短信还是邮件
 * ------sendSMS
 * ------sendEmail
 *
 * 6 两个静态同步方法，2部手机，先打印短信还是邮件
 * ------sendSMS
 * ------sendEmail
 *
 * 7 1个静态同步方法,1个普通同步方法，1部手机，先打印短信还是邮件
 * ------sendEmail
 * ------sendSMS
 *
 * 8 1个静态同步方法,1个普通同步方法，2部手机，先打印短信还是邮件
 * ------sendEmail
 * ------sendSMS
 */


public class Lock_8 {
    public static void main(String[] args) throws Exception {

        Phone phone = new Phone();
        Phone phone2 = new Phone();

        new Thread(() -> {
            try {
                phone.sendSMS();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "AA").start();

        Thread.sleep(100);

        new Thread(() -> {
            try {
               // phone.sendEmail();
               // phone.getHello();
                phone2.sendEmail();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }, "BB").start();
    }
}

```

> **结论:**
> 一个对象里面如果有多个 synchronized 方法，某一个时刻内，只要一个线程去调用其中的一个 synchronized 方法了，其它的线程都只能等待，换句话说，某一个时刻内，只能有唯一一个线程去访问这些 synchronized 方法锁的是当前对象 this，被锁定后，其它的线程都不能进入到当前对象的其它的synchronized 方法。加个普通方法后发现和同步锁无关换成两个对象后，不是同一把锁了，情况立刻变化。
>
> synchronized 实现同步的基础：Java 中的每一个对象都可以作为锁。具体表现为以下 3 种形式：
>
> - 对于普通同步方法，锁是当前实例对象。
> - 对于静态同步方法，锁是当前类的 Class 对象。
> - 对于同步方法块，锁是 Synchonized 括号里配置的对象。
>
> 当一个线程试图访问同步代码块时，它首先必须得到锁，退出或抛出异常时必须释放锁。也就是说如果一个实例对象的非静态同步方法获取锁后，该实例对象的其他非静态同步方法必须等待获取锁的方法释放锁后才能获取锁，可是别的实例对象的非静态同步方法因为跟该实例对象的非静态同步方法用的是不同的锁，所以无需等待该实例对象已获取锁的非静态同步方法释放锁就可以获取他们自己的锁。所有的静态同步方法用的也是同一把锁——类对象本身，这两把锁是两个不同的对象，所以静态同步方法与非静态同步方法之间是不会有竞态条件的。但是一旦一个静态同步方法获取锁后，其他的静态同步方法都必须等待该方法释放锁后才能获取锁，而不管是同一个实例对象的静态同步方法之间，还是不同的实例对象的静态同步方法之间，只要它们同一个类的实例对象！



#### 总结

![](http://image.xiaobailx.top/images/20211119101453.png)