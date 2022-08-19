# Android事件机制

## 基于监听事件的事件处理

### 监听事件处理模型

事件源、事件、事件监听器

### 实现方式

内部类实现监听器

外部类实现监听器

Activity实现监听器

Lambda表达式实现监听器



## 基于回调事件的事件处理

### 回调机制与监听机制

对于回调事件处理模型来说，事件源与事件监听器是统一的。当用户在 GUI 组件上激发某个处理方法时，组件自己的特定方法将会负责处理该事件。

为了实现回调机制的事件处理，通常需要继承 GUI 组件类，并重写该类的事件处理方法。



### 基于回调的传播

几乎所有基于回调的事件的处理方法都会有一个 boolean 类型的返回值，该返回值用于标识该处理方法是否会完全处理该事件。

- true：表示该处理方法已完全处理该事件，该事件不会传播出去。
- false：表示该处理方法并未完全处理该事件，该事件会传播出去。

对于基于回调的事件传播而言，某组件所发生的事件不仅会激发该组件上的回调方法，也会触发该组件所在 Activity 的回调方式——只要事件能够传播到 Activity。

**回调顺序：** 最先触发该组件绑定的事件监听器，然后触发该组件提供的回调方法，最后传播到该组件所在的 Activity



## 系统设置事件

### Configuration类

Configuration 类专门用于描述手机设备的配置信息，包括用户特定的配置信息和系统动态配置信息。

```java
Configuration configuration = getResource().getConfiguration();
```

基本设备配置：

| 配置属性           | 说明                                                         |
| ------------------ | ------------------------------------------------------------ |
| float fontScale    | 获取当前用户设置的字体缩放因子                               |
| int keyboard       | 获取当前设备所关联的键盘类型，返回值包括：KEYBOARD_NOKEYS、KEYBOARD_QWERTY、KEYBOARD_12KEY |
| int keyboardHidden | 标识当前键盘是否可用，返回值包括：KEYBOARDHIDDEN_NO、KEYBOARDHIDDEN_YES |
| Local local        | 获取用户当前的local                                          |
| int mcc            | 获取移动信号的国家码                                         |
| int mnc            | 获取移动信号的网络码                                         |
| int navgation      | 判断系统上方向导航设备类型，返回值包括：NAVIGATION_NONAV、NAVIGATION_DPAD、NAVIGATION_TRACKBALL、NAVIGATION_WHEEL |
| int orientation    | 获取系统屏幕方向，返回值包括：ORIENTATION_LANDSCAPE、ORIENTATION_PORTRAIT、ORIENTATION_SQUARE |
| int touchscreen    | 获取系统触摸屏的触摸方式，返回值包括：TOUCHSCREEN_NOTOUCH、TOUCHSCREEN_STYLEUS、TOUCHSCREEN_FINGER |



### onConfiguration方法

onConfiguraion() 方法是基于回到的事件处理方法，用于监听系统设置的更改。

为了在程序中动态更改系统设置，可以通过调用 Activity 的 setRequestOritation(int) 方法来修改屏幕的方向。

为了使该 Activity 能够监听屏幕方向更改事件，需要在配置该 Activity 时指定 android:configChanges 属性，该属性可支持 mcc、mnc、local、touchscreen、keyboard、keyboardHidden、navigation、orientation、screenLayout、uiMode、screenSize、smallestScreenSize、fontScale 等属性。

 ```java
public class ConfigurationActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_configuration);
        ....
        swi.setOnClickListener(view->{
            if (config.orientation==Configuration.ORIENTATION_LANDSCAPE){
                Log.i("screen","to SCREEN_ORIENTATION_PORTRAIT");
                this.setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT);
            }

            if (config.orientation==Configuration.ORIENTATION_PORTRAIT){
                Log.i("screen","to SCREEN_ORIENTATION_LANDSCAPE");
                this.setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_LANDSCAPE);
            }
        });
    }


    @Override
    public void onConfigurationChanged(Configuration newConfig){
        super.onConfigurationChanged(newConfig);
        String screen = config.orientation==Configuration.ORIENTATION_LANDSCAPE?"横向屏幕":"纵向屏幕";
        Toast.makeText(this,"系统屏幕发生改变"+"\n修改后方向为:"+screen,Toast.LENGTH_LONG).show();
    }
}
 ```



```xml
<activity
    android:name=".configuration.ConfigurationActivity"
    android:configChanges="orientation|screenSize"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```



## Handler消息传递机制

### Handler类简介

Handler 类的两个组要作用：

1. 在线程中发送消息；
2. 在主线程中获取、处理消息。

Handler 类包含以下方法用于发送、处理消息：

```java
//处理消息方法，常用于被重写
handleMessage(Message msg);
//检查消息对垒中是否包含what属性指定的消息
hasMessages(int what);
//检查消息队列中是否包含what属性为指定值且object为指定对象的消息
hasMessages(int what,Object obj);
//获取消息
Message obtainMessage();
//发送空消息
sendEmptyMessage(int what);
//指定延迟多少毫秒后发送空消息
sendEmptymessageDelayment(int what, long delayMillis);
//立即发送消息
sendMessage(Message msg);
//指定延迟多少毫秒后发送消息
sendMessageDelayed(Message msg,long delayMillis);
```

使用方式：

```java
public class HandlerActivity extends AppCompatActivity {

    private ImageView imageView;

    static class MyHandler extends Handler{
        private WeakReference<HandlerActivity> handlerActivity;

        public MyHandler(WeakReference<HandlerActivity> handlerActivity){
            this.handlerActivity = handlerActivity;
        }

        //定义周期性显示的图片
        private int[] images = new int[]{R.drawable.image1,R.drawable.image2,R.drawable.image3,R.drawable.image4,R.drawable.image5};

        private int current;

        @Override
        public void handleMessage(Message msg) {
            //如果消息是本程序发送的
            if (msg.what==0x1233){
                //动态修改所显示的图片
                handlerActivity.get().imageView.setImageResource(
                        images[current++%images.length]
                );
            }
        }
    }

    private MyHandler myHandler = new MyHandler(new WeakReference(this));

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_handler);

        imageView = findViewById(R.id.show);
        //定义定时器,让该计时器周期性执行指定任务
        new Timer().schedule(new TimerTask() {
            @Override
            public void run() {
                //发送消息
                myHandler.sendEmptyMessage(0x1233);
            }
        },0,1200);
    }
}
```



### Handler的工作原理

三大组件：Message、MessageQueue、Looper

Message：Handler 接收和处理的消息对象；

Looper：每个线程只有一个 Looper，负责读取 MessageQueue 中的消息；

MessageQueue：采用先进先方式管理 Message。

程序在创建 Looper 对象时，会同时创建 MessageQueue 对象。程序员无法通过构造器创建 Looper 对象。

```java
private Looper(){
    mQueue = new MessageQueue();
    mRun = true;
    mThread = Thread.currentThread();
}
```

使用 Handler 需要保证当前线程中有一个 Looper 对象，分为两种情况：

- 在主 UI 线程中，系统初始化了一个 Looper 对象，因此程序中可以直接创建 Handler 即可。

- 程序自己启动子线程，必须自己创建一个 Looper 对象，并启动它。创建 Looper 对象调用的静态方法 prepare() 即可，启动时需调用静态方法 looper()。

```java
class CalThread extends Thread{
    private Handler handler;
    @Override
    public void run() {
        Looper.prepare();                                                                                                                 
        handler = new Handler(){
            @Override
            public void handleMessage(Message msg) {
                ...
            }
        };
        Looper.loop();
    }
}
```

prepare() 方法保证每个线程只有一个 Looper 对象，prepare() 方法的源码如下：

```java
private static void prepare(boolean quitAllowed) {
    if (sThreadLocal.get() != null) {
        throw new RuntimeException("Only one Looper may be created per thread");
    }
    sThreadLocal.set(new Looper(quitAllowed));
}
```

loop() 方法使用死循环不断取出 MessageQueue 中的消息，并将取出的消息分给该消息对应的 Handler 进行处理。loop() 方法的核心源码如下：

```java
public static void loop() {

        for (;;) {
            Message msg = queue.next(); // might block
            if (msg == null) {
                // No message indicates that the message queue is quitting.
                return;
            }

            // This must be in a local variable, in case a UI event sets the logger
            final Printer logging = me.mLogging;
            if (logging != null) {
                logging.println(">>>>> Dispatching to " + msg.target + " " +
                        msg.callback + ": " + msg.what);
            }

            msg.target.dispatchMessage(msg);

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



## AsyncTask异步任务

为了解决新线程无法跟新 UI 问题，Android 提供了如下几种解决方案：

1. 使用 Handler 实现线程之间的通信；
2. Activity.runOnUiThread(Runnble);
3. View.post(Runnable);
4. View.postDelayed(Runnable, long);

以上实现方式较为繁琐，异步任务则可以进一步简化这种操作，适用于简单的异步任务。AsyncTask<Params,Progress,Result> 是一个抽象类，通常用于被继承，继承时需要指定以下三个泛型参数：

```txt
Params：启动任务执行的输入参数类型
Progress：后台任务完成的进度值的类型
Result：后台执行任务完成后返回结果的类型
```

### 实现步骤

1. 创建 AsyncTask 子类，并为三个泛型参数指定类型，如某个泛型无需指定参数，可将其指定为 void
2. 根据需要实现 AysncTask 如下方法：

```java
//后台线程将要完成的任务，可调用pulishProgress(Progress... values)方法更新任务的执行进度
doInBackground(Params... params);
//在pulishProgress(Progress... values)方法被调用后会触发该方法
onProgressUpdate(Params... values);
//在执行doInBackground()前被调用，一般用于完成一些初始化的准备工作
onPreExecute();
//当doInBackground()执行完后被系统调用，同时将doInBackground()的返回值传给该方法
onPostExecute(Result result);
```

3. 调用 AsyncTask 子类实例的 execute(Params... params) 方法

使用 AsyncTask 必须遵守如下规则：

1. 必须在 UI 线程中创建 AsyncTask 实例；
2. 必须在 UI 线程中调用 AsyncTask 的 execute() 方法；
3. AsyncTask 中的 onPreExecute()、onPostExecute(Result result)、doInBackground(Params... params)、onProgressUpdate(Progress... values)方法，由系统自动调用；
4. 每个 AsyncTask 只能被执行一次，多次调用将会引发异常。

```java
class DownLoadTask extends AsyncTask<URL,Integer,String>{
        //执行任务
        @Override
        protected String doInBackground(URL... urls) {
            return null;
        }
		//在doInBackground(URL... urls)被调用之前触发
        @Override
        protected void onPreExecute() {
            super.onPreExecute();
        }
		//在doInBackground(URL... urls)执行完后被调用
        @Override
        protected void onPostExecute(String s) {
        }
		//在pulishProgress(Progress... values)方法被调用后会触发该方法
        @Override
        protected void onProgressUpdate(Integer... values) {
        }
    }
```



