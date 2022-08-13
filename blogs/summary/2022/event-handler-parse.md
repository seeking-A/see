# Android消息机制解析

###　为什么主线程中可以直接使用 Handler ?

Handler 的运行需要底层的 MessageQueue 和 Looper 支撑，MessageQueue 是以单链表为数据结构的消息列表，Looper 以无限循环的形式去查找 MessageQueue 中是否有新消息需要处理。Looper 中还有一个特殊概念 ThreadLocal，可以在不同的线程中互补干扰地存储并提供数据，通过 ThreadLocal 可以轻松获取每个线程的 Looper。线程默认没有 Looper，如果需要使用 Handler 就必须为线程创建 Looper。而主线程，也就是 ActivityThread，它在创建时会初始化 Looper，这就是在主线程中默认可以使用 Handler 的原因。



### 为什么Android会提供Handler？

Android 规定访问 UI 只能在主线程中进行，如果子线程中访问 UI，那么程序就会抛出异常。ViewRootImpl 对 UI 操作做了验证，该验证过程是由 checkThread 方法完成的：

```java
void checkThread(){
    if(mThread != Thread.currentThrad){
        throw new CalledFromWrongThreadException(
        "Only the original thread that crated a view hierarchy can touch its views");
    }
}
```

同时 Android 又建议在主线程中不要进行耗时操作，否则导致程序无法响应即 ANR。而在系统提供 Handler，正是为了解决子线程中无法访问 UI 的矛盾。



### 为什么不允许子线程中访问UI呢？

这是因为 Android 的 UI 控件时线程不安全的，如果多线程中并发访问可能导致 UI 控件处于不可预期的状态。



### 为什么系统不对UI的访问加上锁机制呢？

首先加上锁机制会让 UI 访问逻辑变得负责，其次锁机制会降低 UI 的访问效率，阻塞某些线程的执行。鉴于以上两个缺点，最简单且高效的方法就是采用单线程模型来处理 UI 组件。



### Handler的处理过程

Handler 创建完毕后，内部的 Looper 及 MessageQueue 就可以和 Handler 一起协同工作。通过 Handler 的 post 方法将一个 Runnable 投递到 Handler 内部的 Looper 中去处理，也可以通过 Handler 的 send 方法发送一个消息，该消息同样会在 Looper 中处理。而 post 方法最终也是通过 send 方法来完成的。

当 Handler 的 send 方法调用时，会调用 MessageQueue 的 enqueueMessage 方法将这个消息放到消息队列中，然后 Looper 发现有消息来时就会处理这个消息，最终消息中的 Runable 或 Handler 的 HandlerMessage 方法就会被调用。而 Looper 是运行在 Handler 所在线程中，这样一来 Handler 中的业务就被切换到创建 Handler 所在的线程中去执行了。

![未命名文件](http://image.xiaobailx.top/images/202208131628253.png)



### ThreadLocal (Java8)

#### 运用场景

1. 存储当前线程的数据
2. 复杂逻辑下的对象传递

#### 内部原理

ThreadLocal 是一个泛型类，其定义为 public class ThreadLocal<T> ，所以弄清楚 ThreadLocal 的 get 和 set 方法就可以明白其工作原理。

先看 set 方法：

```java
 public void set(T value) {
     //1、获取当前线程
     Thread t = Thread.currentThread();
     //2、获取线程中的属性 threadLocalMap ,如果threadLocalMap 不为空，
     //则直接更新要保存的变量值，否则创建threadLocalMap，并赋值
     ThreadLocalMap map = getMap(t);
     if (map != null)
         map.set(this, value);
     else
         // 初始化thradLocalMap 并赋值
         createMap(t, value);
 }
```

ThreadLocalMap 是 ThreadLocal 的内部静态类，而它的构成主要是用 Entry 来保存数据 ，而且还是继承的弱引用。在 Entry 内部使用 ThreadLocal 作为 key，使用我们设置的 value 作为 value。

```java
static class ThreadLocalMap {
 /**
   * The entries in this hash map extend WeakReference, using
   * its main ref field as the key (which is always a
   * ThreadLocal object).  Note that null keys (i.e. entry.get()
   * == null) mean that the key is no longer referenced, so the
   * entry can be expunged from table.  Such entries are referred to
   * as "stale entries" in the code that follows.
   */
    static class Entry extends WeakReference<ThreadLocal<?>> {
        /** The value associated with this ThreadLocal. */
        Object value;
        Entry(ThreadLocal<?> k, Object v) {
            super(k);
            value = v;
        }
    }
}
```

```java
//这个是threadlocal 的内部方法
void createMap(Thread t, T firstValue) {
        t.threadLocals = new ThreadLocalMap(this, firstValue);
}
 
//ThreadLocalMap 构造方法
ThreadLocalMap(ThreadLocal<?> firstKey, Object firstValue) {
    table = new Entry[INITIAL_CAPACITY];
    int i = firstKey.threadLocalHashCode & (INITIAL_CAPACITY - 1);
    table[i] = new Entry(firstKey, firstValue);
    size = 1;
    setThreshold(INITIAL_CAPACITY);
}
```

ThreadLocalMap 其实是 Thread 线程的一个属性值，而 ThreadLocal 是维护 ThreadLocalMap。ThreadLocal 的 get 方法如下：

```java
public T get() {
    //1、获取当前线程
    Thread t = Thread.currentThread();
    //2、获取当前线程的ThreadLocalMap
    ThreadLocalMap map = getMap(t);
    //3、如果map数据为空，
    if (map != null) {
        //3.1、获取threalLocalMap中存储的值
        ThreadLocalMap.Entry e = map.getEntry(this);
        if (e != null) {
            @SuppressWarnings("unchecked")
            T result = (T)e.value;
            return result;
        }
    }
    //如果是数据为null，则初始化，初始化的结果，TheralLocalMap中存放key值为threadLocal，值为null
    return setInitialValue();
}


private T setInitialValue() {
    T value = initialValue();
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
    return value;
}
```

参考博客：[史上最全ThreadLocal 详解（一）](https://blog.csdn.net/u010445301/article/details/111322569)



### MessageQueue工作原理

MessageQueue 组要包含两个操作：插入和读取。读取操作本身伴随着删除操作，插入和读取对应的方法分别为 enqueueMessage 和 next。MessageQueue 是通过一个单链表的数据结构来维护消息列表，在插入和删除上比较有优势。

enqueueMessage 源码如下：

```java
boolean enqueueMessage(Message msg, long when) {
    ...
    synchronized (this) {
        ...
        msg.markInUse();
        msg.when = when;
        Message p = mMessages;
        boolean needWake;
        if (p == null || when == 0 || when < p.when) {
            // New head, wake up the event queue if blocked.
            msg.next = p;
            mMessages = msg;
            needWake = mBlocked;
        } else {
            // Inserted within the middle of the queue.  Usually we don't have to wake
            // up the event queue unless there is a barrier at the head of the queue
            // and the message is the earliest asynchronous message in the queue.
            needWake = mBlocked && p.target == null && msg.isAsynchronous();
            Message prev;
            for (;;) {
                prev = p;
                p = p.next;
                if (p == null || when < p.when) {
                    break;
                }
                if (needWake && p.isAsynchronous()) {
                    needWake = false;
                }
            }
            msg.next = p; // invariant: p == prev.next
            prev.next = msg;
        }

        // We can assume mPtr != 0 because mQuitting is false.
        if (needWake) {
            nativeWake(mPtr);
        }
    }
    return true;
}
```

next 源码如下：

```java
Message next() {
        ...
        int pendingIdleHandlerCount = -1; // -1 only during first iteration
        int nextPollTimeoutMillis = 0;
        for (;;) {
            if (nextPollTimeoutMillis != 0) {
                Binder.flushPendingCommands();
            }

            nativePollOnce(ptr, nextPollTimeoutMillis);

            synchronized (this) {
                // Try to retrieve the next message.  Return if found.
                final long now = SystemClock.uptimeMillis();
                Message prevMsg = null;
                Message msg = mMessages;
                if (msg != null && msg.target == null) {
                    // Stalled by a barrier.  Find the next asynchronous message in the queue.
                    do {
                        prevMsg = msg;
                        msg = msg.next;
                    } while (msg != null && !msg.isAsynchronous());
                }
                if (msg != null) {
                    if (now < msg.when) {
                        // Next message is not ready.  Set a timeout to wake up when it is ready.
                        nextPollTimeoutMillis = (int) Math.min(msg.when - now, Integer.MAX_VALUE);
                    } else {
                        // Got a message.
                        mBlocked = false;
                        if (prevMsg != null) {
                            prevMsg.next = msg.next;
                        } else {
                            mMessages = msg.next;
                        }
                        msg.next = null;
                        if (DEBUG) Log.v(TAG, "Returning message: " + msg);
                        msg.markInUse();
                        return msg;
                    }
                } else {
                    // No more messages.
                    nextPollTimeoutMillis = -1;
                }
                ....
            }
            ....
        }
    }
```



### Looper工作原理

Looper 在 Android 消息机制中扮演着消息循环角色，它会不停从 Message 中查看是否有新消息进行处理，没有则一直阻塞。其构造函数如下：

```java
private Looper(boolean quitAllowed) {
    mQueue = new MessageQueue(quitAllowed);
    mThread = Thread.currentThread();
}
```

Handler 工作需要 Looper，一个线程通过 Looper.prepare() 来创建一个 Looper，接着通过 Looper.loop() 来开启消息循环。

```java
new Thread("ThreadName"){
    @Override
    public void run(){
        Looper.prepare();
        Handler handler = new Handler();
        Looper.looper();
    }
}.start();
```

Looper 除了 prepare 方法外，还提供了 prepareMainLooper 方法，该方法主要是给 ActivityThread 创建 Looper 使用的，本质也是通过 prepare 来实现的。此外，Looper 还提供了一个 getMainLooper 方法，通过它可以在任何地方获取到主线程的 Looper。Looper 也是可以退出的，它提供了 quit 和 quitSafely 来退出一个 Looper，两者区别在于 quit 会直接退出，而 quickSafely 只是设定一个退出标识，然后把消息队列的已有消息处理完毕后才安全退出。

Looper 在调用 loop 方法后，消息循环系统才会真正起作用，其实现如下所示：

```java
    /**
     * Run the message queue in this thread. Be sure to call
     * {@link #quit()} to end the loop.
     */
    public static void loop() {
        final Looper me = myLooper();
        if (me == null) {
            throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
        }
        final MessageQueue queue = me.mQueue; 

        // Make sure the identity of this thread is that of the local process,
        // and keep track of what that identity token actually is.
        Binder.clearCallingIdentity();
        final long ident = Binder.clearCallingIdentity();

        // Allow overriding a threshold with a system prop. e.g.
        // adb shell 'setprop log.looper.1000.main.slow 1 && stop && start'
        final int thresholdOverride =
                SystemProperties.getInt("log.looper."
                        + Process.myUid() + "."
                        + Thread.currentThread().getName()
                        + ".slow", 0);

        boolean slowDeliveryDetected = false;

        for (;;) {
            //queue.next()是一个阻塞方法，没有消息时一直阻塞
            Message msg = queue.next(); // might block
            if (msg == null) {
                //消息为空时则跳出循环		
                return;
            }

            // This must be in a local variable, in case a UI event sets the logger
            final Printer logging = me.mLogging;
            if (logging != null) {
                logging.println(">>>>> Dispatching to " + msg.target + " " +
                        msg.callback + ": " + msg.what);
            }

            final long traceTag = me.mTraceTag;
            long slowDispatchThresholdMs = me.mSlowDispatchThresholdMs;
            long slowDeliveryThresholdMs = me.mSlowDeliveryThresholdMs;
            if (thresholdOverride > 0) {
                slowDispatchThresholdMs = thresholdOverride;
                slowDeliveryThresholdMs = thresholdOverride;
            }
            final boolean logSlowDelivery = (slowDeliveryThresholdMs > 0) && (msg.when > 0);
            final boolean logSlowDispatch = (slowDispatchThresholdMs > 0);

            final boolean needStartTime = logSlowDelivery || logSlowDispatch;
            final boolean needEndTime = logSlowDispatch;

            if (traceTag != 0 && Trace.isTagEnabled(traceTag)) {
                Trace.traceBegin(traceTag, msg.target.getTraceName(msg));
            }

            final long dispatchStart = needStartTime ? SystemClock.uptimeMillis() : 0;
            final long dispatchEnd;
            try {
                //msg.target为发送该消息的Handler对象
                //发送的消息最终通过dispatchMessage()进行处理
                msg.target.dispatchMessage(msg);
                dispatchEnd = needEndTime ? SystemClock.uptimeMillis() : 0;
            } finally {
                if (traceTag != 0) {
                    Trace.traceEnd(traceTag);
                }
            }
            if (logSlowDelivery) {
                if (slowDeliveryDetected) {
                    if ((dispatchStart - msg.when) <= 10) {
                        Slog.w(TAG, "Drained");
                        slowDeliveryDetected = false;
                    }
                } else {
                    if (showSlowLog(slowDeliveryThresholdMs, msg.when, dispatchStart, "delivery",
                            msg)) {
                        // Once we write a slow delivery log, suppress until the queue drains.
                        slowDeliveryDetected = true;
                    }
                }
            }
            if (logSlowDispatch) {
                showSlowLog(slowDispatchThresholdMs, dispatchStart, dispatchEnd, "dispatch", msg);
            }

            if (logging != null) {
                logging.println("<<<<< Finished to " + msg.target + " " + msg.callback);
            }

            // Make sure that during the course of dispatching the
            // identity of the thread wasn't corrupted.
            final long newIdent = Binder.clearCallingIdentity();
            if (ident != newIdent) {
                Log.wtf(TAG, "Thread identity changed from 0x"
                        + Long.toHexString(ident) + " to 0x"
                        + Long.toHexString(newIdent) + " while dispatching to "
                        + msg.target.getClass().getName() + " "
                        + msg.callback + " what=" + msg.what);
            }

            msg.recycleUnchecked();
        }
    }
```



### Handler工作原理

Handler 的工作主要包含消息的发送和接收过程，消息的发送通过 post 的一系列方法以及 send 的一系列方法来实现。

```java
public final boolean sendMessageDelayed(Message msg, long delayMillis)
    {
        if (delayMillis < 0) {
            delayMillis = 0;
        }
        return sendMessageAtTime(msg, SystemClock.uptimeMillis() + delayMillis);
    }

  
    public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
        MessageQueue queue = mQueue;
        if (queue == null) {
            RuntimeException e = new RuntimeException(
                    this + " sendMessageAtTime() called with no mQueue");
            Log.w("Looper", e.getMessage(), e);
            return false;
        }
        return enqueueMessage(queue, msg, uptimeMillis);
    }

    
    public final boolean sendMessageAtFrontOfQueue(Message msg) {
        MessageQueue queue = mQueue;
        if (queue == null) {
            RuntimeException e = new RuntimeException(
                this + " sendMessageAtTime() called with no mQueue");
            Log.w("Looper", e.getMessage(), e);
            return false;
        }
        return enqueueMessage(queue, msg, 0);
    }
```

可以发现，Handler 发送消息的过程仅仅时向消息对列中插入了一条消息，MessageQueue 的 next 方法就会返回这条消息给 Looper，Looper 接收后开始处理，最终消息由 Looper 交由 Handler 处理，即 Handler 的 dispatchMessage 方法调用。

```java
public void dispatchMessage(Message msg) {
        if (msg.callback != null) {
            handleCallback(msg);
        } else {
            if (mCallback != null) {
                if (mCallback.handleMessage(msg)) {
                    return;
                }
            }
            handleMessage(msg);
        }
    }
```

Message 的 callback 是一个 Runnable 对象，实际上就是 Handler 的 post 方法传递的 Runnable 参数。handleCallback 的逻辑如下：

```java
 private static void handleCallback(Message message) {
     message.callback.run();
 }
```

当 mCallback 不为 null 时就调用 mCallback 的 handleMessage 方法来处理消息，其定义如下：

```java
    /**
     * Callback interface you can use when instantiating a Handler to avoid
     * having to implement your own subclass of Handler.
     */
    public interface Callback {
        /**
         * @param msg A {@link android.os.Message Message} object
         * @return True if no further handling is desired
         */
        public boolean handleMessage(Message msg);
    }
```

Handler 还有一个特殊的构造方法，就是通过一个特定的 Looper 来构造 Handler，其实现如下：

```java
public Handler(Looper looper){
    this(looper,null,false);
}
```

Handler 的默认构造方法 public Handler() 会调用如下的构造方法，解释了在没有 Looper 的子线程中会引发程序异常的原因。

```java
public Handler(Callback callback, boolean async) {
       ...
        mLooper = Looper.myLooper();
        if (mLooper == null) {
            throw new RuntimeException(
                "Can't create handler inside thread " + Thread.currentThread()
                        + " that has not called Looper.prepare()");
        }
        mQueue = mLooper.mQueue;
        mCallback = callback;
        mAsynchronous = async;
    }
```



参考书籍：《Android开发艺术探索》/任玉刚 | 电子工业出版社
