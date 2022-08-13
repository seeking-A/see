---
title: CompletableFuture
date: 2021-11-21
tags:
 - JUC
categories:
 - 高并发编程
---

## completableFuture 简介

> CompletableFuture 在 Java 里面被用于异步编程，异步通常意味着非阻塞，可以使得我们的任务单独运行在与主线程分离的其他线程中，并且通过回调可以在主线程中得到异步任务的执行状态，是否完成，和是否异常等信息。CompletableFuture 实现了 Future, CompletionStage 接口，实现了 Future 接口就可以兼容现在有线程池框架，而 CompletionStage 接口才是异步编程的接口抽象，里面定义多种异步方法，通过这两者集合，从而打造出了强大的 CompletableFuture 类。



##  Future 与 CompletableFuture

> Futrue 在 Java 里面，通常用来表示一个异步任务的引用，比如我们将任务提交到线程池里面，然后我们会得到一个 Futrue，在 Future 里面有 isDone() 方法来 判断任务是否处理结束，还有 get() 方法可以一直阻塞直到任务结束然后获取结果，但整体来说这种方式，还是同步的，因为需要客户端不断阻塞等待或者不断轮询才能知道任务是否完成。
>
>  **Future 的主要缺点如下：**
>
> - 不支持手动完成
>
>   我提交了一个任务，但是执行太慢了，我通过其他路径已经获取到了任务结果，现在没法把这个任务结果通知到正在执行的线程，所以必须主动取消或者一直等待它执行完成。
>
> - 不支持进一步的非阻塞调用
>
>   通过 Future 的 get() 方法会一直阻塞到任务完成，但是想在获取任务之后执行额外的任务，因为 Future 不支持回调函数，所以无法实现这个功能。
>
> - 不支持链式调用
>
>   对于 Future 的执行结果，我们想继续传到下一个 Future 处理使用，从而形成一个链式的 pipline 调用，这在 Future 中是没法实现的。
>
> - 不支持多个 Future 合并
>
>   比如我们有 10 个 Future 并行执行，我们想在所有的 Future 运行完毕之后，执行某些函数，是没法通过 Future 实现的。
>
> - 不支持异常处理
>
>   Future 的 API 没有任何的异常处理的 API，所以在异步运行时，如果出了问题是不好定位的。



## CompletableFuture 入门

### 使用 CompletableFuture

场景:主线程里面创建一个 CompletableFuture，然后主线程调用 get() 方法会阻塞，最后我们在一个子线程中使其终止。

```java
/**
 * 主线程里面创建一个 CompletableFuture，然后主线程调用 get() 方法会阻塞，最后我们在一个子线程中使其终止
 * @param args
 */
public static void main(String[] args) throws Exception{
    CompletableFuture<String> future = new CompletableFuture<>();
    new Thread(() -> {
        try{
            System.out.println(Thread.currentThread().getName() + "子线程开始干活");
            //子线程睡 5 秒
            Thread.sleep(5000);
            //在子线程中完成主线程
            future.complete("success");
        }catch (Exception e){
            e.printStackTrace();
        }
    }, "A").start();
    //主线程调用 get 方法阻塞
    System.out.println("主线程调用 get 方法获取结果为: " + future.get());
    System.out.println("主线程完成,阻塞结束!!!!!!");
}
```



### 没有返回值的异步任务

```java
/**
 * 没有返回值的异步任务
 * @param args
 */
public static void main(String[] args) throws Exception{
    System.out.println("主线程开始");
    //运行一个没有返回值的异步任务
    CompletableFuture<Void> future = CompletableFuture.runAsync(() -> {
        try {
            System.out.println("子线程启动干活");
            Thread.sleep(5000);
            System.out.println("子线程完成");
        } catch (Exception e) {
            e.printStackTrace();
        }
    });
    //主线程阻塞
    future.get();
    System.out.println("主线程结束");
}
```



### 有返回值的异步任务

```java
/**
 * 有返回值的异步任务
 * @param args
 */
public static void main3(String[] args) throws Exception{
    System.out.println("主线程开始");
    //运行一个有返回值的异步任务
    CompletableFuture<String> future =
            CompletableFuture.supplyAsync(() -> {
                try {
                    System.out.println("子线程开始任务");
                    Thread.sleep(5000);
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return "子线程完成了!";
            });
    //主线程阻塞
    String s = future.get();
    System.out.println("主线程结束, 子线程的结果为:" + s);
}
```



### 线程依赖

```java
private static Integer num = 10;
/**
 * 先对一个数加 10,然后取平方
 * @param args
 */
public static void main(String[] args) throws Exception {
    System.out.println("主线程开始");
    CompletableFuture<Integer> future =
            CompletableFuture.supplyAsync(() -> {
                try {
                    System.out.println("加 10 任务开始");
                    num += 10;
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return num;
            }).thenApply(integer -> {
                return num * num;
            });
    Integer integer = future.get();
    System.out.println("主线程结束, 子线程的结果为:" + integer);
}
```



### 消费处理结果

```java
private static Integer num = 10;
/**
 * thenAccept 消费处理结果, 接收任务的处理结果，并消费处理，无返回结果。 
 */
public static void main(String[] args) throws Exception{
    System.out.println("主线程开始");
    CompletableFuture.supplyAsync(() -> {
        try {
            System.out.println("加 10 任务开始");
            num += 10;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return num;
    }).thenApply(integer -> {
        return num * num;
    }).thenAccept(new Consumer<Integer>() {
        @Override
        public void accept(Integer integer) {
            System.out.println("子线程全部处理完成,最后调用了 accept,结果为:" +
                    integer);
        }
    });
}
```



### 异常处理

exceptionally 异常处理,出现异常时触发。

```java
public static void main(String[] args) throws Exception{
    System.out.println("主线程开始");
    CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {
        int i= 1/0;
        System.out.println("加 10 任务开始");
        num += 10;
        return num;
    }).exceptionally(ex -> {
        System.out.println(ex.getMessage());
        return -1;
    });
    System.out.println(future.get());
}
```



handle 类似于 thenAccept/thenRun 方法,是最后一步的处理调用,但是同时可以处理异常。

```java
public static void main8(String[] args) throws Exception{
    System.out.println("主线程开始");
    CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {
        System.out.println("加 10 任务开始");
        num += 10;
        return num;
    }).handle((i,ex) -> {
        System.out.println("进入 handle 方法");
        if (ex != null) {
            System.out.println("发生了异常,内容为:" + ex.getMessage());
            return -1;
        } else {
            System.out.println("正常完成,内容为: " + i);
            return i;
        }
    });
    System.out.println(future.get());
}	
```

​	

### 结果合并

thenCompose 合并两个有依赖关系的 CompletableFutures 的执行结果。

```java
public static void main(String[] args) throws Exception{
    System.out.println("主线程开始");
    //第一步加 10
    CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {
        System.out.println("加 10 任务开始");
        num += 10;
        return num;
    });
    //合并
    CompletableFuture<Integer> future1 = future.thenCompose(i ->
            //再来一个 CompletableFuture
            CompletableFuture.supplyAsync(() -> {
                return i + 1;
            }));
    System.out.println(future.get());
    System.out.println(future1.get());
}
```



thenCombine 合并两个没有依赖关系的 CompletableFutures 任务。

```java
/**
 * thenCombine 合并两个没有依赖关系的 CompletableFutures 任务 
 */
public static void main(String[] args) throws Exception{
    System.out.println("主线程开始");
    CompletableFuture<Integer> job1 = CompletableFuture.supplyAsync(() -> {
        System.out.println("加 10 任务开始");
        num += 10;
        return num;
    });
    CompletableFuture<Integer> job2 = CompletableFuture.supplyAsync(() -> {
        System.out.println("乘以 10 任务开始");
        num = num * 10;
        return num;
    });
    //合并两个结果
    CompletableFuture<Object> future = job1.thenCombine(job2, new
            BiFunction<Integer, Integer, List<Integer>>() {
                @Override
                public List<Integer> apply(Integer a, Integer b) {
                    List<Integer> list = new ArrayList<>();
                    list.add(a);
                    list.add(b);
                    return list;
                }
            });
    System.out.println("合并结果为:" + future.get());
}
```



合并多个任务的结果 allOf 与 anyOf

**allOf：** 一系列独立的 future 任务，等其所有的任务执行完后做一些事情。

```java
/**
 * 先对一个数加 10,然后取平方
 * @param args
 */
public static void main11(String[] args) throws Exception{
    System.out.println("主线程开始");
    List<CompletableFuture> list = new ArrayList<>();
    CompletableFuture<Integer> job1 = CompletableFuture.supplyAsync(() -> {
        System.out.println("加 10 任务开始");
        num += 10;
        return num;
    });
    list.add(job1);
    CompletableFuture<Integer> job2 = CompletableFuture.supplyAsync(() -> {
        System.out.println("乘以 10 任务开始");
        num = num * 10;
        return num;
    });
    list.add(job2);
    CompletableFuture<Integer> job3 = CompletableFuture.supplyAsync(() -> {
        System.out.println("减以 10 任务开始");
        num = num * 10;
        return num;
    });
    list.add(job3);
    CompletableFuture<Integer> job4 = CompletableFuture.supplyAsync(() -> {
        System.out.println("除以 10 任务开始");
        num = num * 10;
        return num;
    });
    list.add(job4);
    //多任务合并
    List<Integer> collect =
            list.stream().map(CompletableFuture<Integer>::join).collect(Collectors.toList());
    System.out.println(collect);
}
```



**anyOf：** 只要在多个 future 里面有一个返回，整个任务就可以结束，而不需要等到每一个future 结束。

```java
/**
 * 先对一个数加 10,然后取平方
 * @param args
 */
public static void main12(String[] args) throws Exception{
    System.out.println("主线程开始");
    CompletableFuture<Integer>[] futures = new CompletableFuture[4];
    CompletableFuture<Integer> job1 = CompletableFuture.supplyAsync(() -> {
        try{
            Thread.sleep(5000);
            System.out.println("加 10 任务开始");
            num += 10;
            return num;
        }catch (Exception e){
            return 0;
        }
    });
    futures[0] = job1;
    CompletableFuture<Integer> job2 = CompletableFuture.supplyAsync(() -> {
        try{
            Thread.sleep(2000);
            System.out.println("乘以 10 任务开始");
            num = num * 10;
            return num;
        }catch (Exception e){
            return 1;
        }
    });
    futures[1] = job2;
    CompletableFuture<Integer> job3 = CompletableFuture.supplyAsync(() -> {
        try{
            Thread.sleep(3000);
            System.out.println("减以 10 任务开始");
            num = num * 10;
            return num;
        }catch (Exception e){
            return 2;
        }
    });
    futures[2] = job3;
    CompletableFuture<Integer> job4 = CompletableFuture.supplyAsync(() -> {
        try{
            Thread.sleep(4000);
            System.out.println("除以 10 任务开始");
            num = num * 10;
            return num;
        }catch (Exception e){
            return 3;
        }
    });
    futures[3] = job4;
    CompletableFuture<Object> future = CompletableFuture.anyOf(futures);
    System.out.println(future.get());
}
```



## 总结

![](http://image.xiaobailx.top/images/20211121130522.png)