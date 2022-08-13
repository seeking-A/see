# Android四大组件

## Activity

### 实现步骤

1. 继承 Activity 或其子类，实现以下方法：

   ```java
   //第一次创建时回调
   protected void onCreate(Bundle savedInstanceState);
   //启动时回调
   protected void onStart();
   //再次启动时回调
   protected void onRestart() ;
   //回到前台时回调
   protected void onResume();
   //转入后台但依然可见时回调
   protected void onPause() ;
   //转入后台完全不可见时回调
   protected void onStop();
   //被系统销毁时回调
   protected void onDestroy();
   ```

2. 在 AndroidMainfest.xml 文件中配置

3. 启动，有以下两种方式：

   ```java
   //启动其他Activity
   startActivity(Intent intent);
   //启动其让Activity并返回请求码
   startActivityForResult(Intent intent,int requestCode);
   ```

4. 停止，有以下两种方式：

   ```java
   //结束当前Activity
   finish();
   //结束当前Activity并返回状态码
   finish(Intent intent,int requestCode);
   ```



### IBindle与Activity之间的通信

IBindle 是一个简单的数据携带包，用于实现 Activity 之间的数据交换。Intent 提供了 putExtras() 和 getExtras() 方法，这些方法实质是存取 Intent 所携带的 IBindle 中的数据。

Intent 提供的多个重载方法携带额外数据：

```java
//向Intent中存放数据包
putExtras(Bundle data);
//取出Intent中存放的数据包
getExtras(Bundle data);
//向Intent中以k-v形式存放数据包
putExtra(String name,String value);
//根据k取出指定类型的值
getXxxExtra(String name);
```

Bundle包含的多个方法来存入数据：

```java
//向Bundle中存放数据
putXxx(String key,Xxx data);
//向Bundle中存放一个可序列化对象
putSerialzable(String key,Serialzable data);
```



### 生命周期

运行状态：当前 Activity 位于前台，用户可见，可以获得焦点；

暂停状态：其他 Activity  位于前台，该 Activity 依然可见，但无法获得焦点；

停止状态：该 Activity 不可见，无法获得焦点；

销毁状态：该 Activity 结束或 Activity 所在进程被结束。



### 四种加载模式

standard：每次启动目标 Activity 时，总会为目标 Activity 创建一个新实例，并添加到原有的 Task 中。

singleTop：当将要启动的目标 Activity 位于 Task 栈顶中，系统直接服用已有的 Activity 实例。

singleTask：包括以下三种情况

1. 启动目标 Activity 不存在，创建新实例并加入到 Task 栈顶中；
2. 启动目标 Activity 存在且位于 Task 栈顶，直接复用已有 Activity 实例；
3. 启动目标 Activity 存在但不在 Task 栈顶，将位于目标 Activity 之上的实例移除 Task。

singleInstance：包括以下两种情况：

1. 启动目标 Activity 不存在，先创建一个 Task，然后创建目标 Activity 实例并添加到 Task 中；
2. 启动目标 Activity 存在，将该 Activity 所在 Task 转到前台，使该 Activity 显示出来。



## ContentProvide

ContentProvide 是不同应用程序之间进行交换的标准 API，以某种 Uri 的形式对外提供数据，允许其他应用访问或修改数据。

### 实现步骤

1. 继承 ContentProvider，实现以下方法：

   ```java
   //当其他程序第一次访问该ContentProvider时，该ContentProvider被创建出来并立即回调onCreate()
   public boolean onCreate();
   //根据Uri查询selection条件所匹配的全部记录，projection指的是列名列表，表明只选择出指定的数据列
   public Cursor query(Uri uri, String[] projection,String selection,String[] selectionArgs,String sortOrder);
   //用于返回当前Uri所代表的MIME类型
   //若Uri对应数据包含多条记录，该MIME类型字符串应以vnd.android.cursor.dir/开头
   //若Uri对应数据包含一条记录，该MIME类型字符串应以vnd.android.cursor.item/开头
   public String getType(Uri uri);
   //根据Uri插入values对应的数据
   public Uri insert(Uri uri, ContentValues values);
   //根据Uri删除selection所匹配的全部记录
   public int delete(Uri uri, String selection,String[] selectionArgs);
   //根据Uri修改selection条件所匹配的全部记录
   public int update(Uri uri, ContentValues values, String selection, String[] selectionArgs);
   ```

2. 向 AndroidMainfest.xml 文件中注册该 ContentProvider，并为它绑定一个 Uri

> Uri类似于互联网中的URL，URL由协议、域名、网站资源三个部分组成，Uri可以分为以下三部分
>
> 1. Content://：暴露和访问ContentProvider的协议默认为content://
> 2. org.crazyit.providers.dictprovider：这部分是ContentProvider的authorities，用于指定要访问的ContentProvider
> 3. words：资源部分
>
> Uri基本遵循了RESTful风格



### ContentResolver

ContentProvider 相当于一个网站，它的作用是暴露可供操作的数据，其他程序则通过 ContentResolve r来操作 ContentProvider 所暴露的数据。ContentResolver 相当于 HttpClient.

Context 提供了以下方法来获取 ContentResolver 对象：

```java
//获取应用的默认ContentResolver
getContentResolver();
//向Uri对应的ContentProvider
insert(Uri url,ContentValues values);
//删除Uri对应的ContentProvider中where条件匹配数据
delete(Uri url,String where,String[] selectionArgs);
//更新Uri对应的ContentProvider中where条件匹配数据
update(Uri url,String where,String[] selectionArgs);
//查询Uri对应的ContentProvider中where条件匹配数据
query(Uri url,String[] projection,String selection,String[] selectionArgs,String sortOrder)
```

ContentProvider 一般为单实例模式，多个程序通过 ContentResolver 操作 ContentProvider 提供数据时，会委托给同一个 ContentProvider



###  Uri 参数判断

为了确定 ContentProvider 能够处理 Uri，以及确定每个方法中 Uri 参数所操作的数据， Android 提供了 UriMatch、ContentUris 工具类。

UriMatcher 工具类

```java
//向UriMatcher对象注册Uri
void addURL(String authority,String path,int code);
//根据前面注册的Uri来判断Uri对应的标识,若无法匹配则返回-1
int match(Uri uri);
```

ContentUris 工具类

```java
//用于为路径添加ID部分
withAppendId(Uri contentUri, long id)
//用于指定Uri中解析出包含ID值
parseId(Uri contentUri);
```



###  系统的ContentProvider

| Uri                                                | 说明                                      |
| -------------------------------------------------- | ----------------------------------------- |
| ContactsContract.Contacts.CONTENT_URL              | 管理联系人的Uri                           |
| ContactsContract.CommonDataKinds.Phone.CONTENT_URL | 管理联系人电话的Uri                       |
| ContactsContract.CommonDataKinds.Email.CONTENT_URL | 管理联系人E-mail的Uri                     |
| MediaStore.Audio.Media.EXTERNAL_CONTENT_URL        | 存储在手机外部存储器上的音频文件内容的Uri |
| MediaStore.Audio.Media.INTERNAL_CONTENT_URL        | 存储在手机内部存储器上的音频文件内容的Uri |
| MediaStore.Images.Media.EXTERNAL_CONTENT_URL       | 存储在手机外部存储器上的图片文件内容的Uri |
| MediaStore.Video.Media.INTERNAL_CONTENT_URL        | 存储在手机内部存储器上的视频文件内容的Uri |
| MediaStore.Video.Media.EXTERNAL_CONTENT_URL        | 存储在手机外部存储器上的视频文件内容的Uri |



## Service

### 实现步骤

1. 继承 Service 重写回调方法

2. 在 Androidmainfest.xml 文件中配置该 Service

3. 运行 Service，包括以下两种方法：

   - Context 的 startService() 方法，该方法启动 Service 与启动者不建立连接关系，启动者退出 Service 保持运行。
   - Centext 的 bindService() 方法，该方法启动 Service 与启动者绑定在一起，启动者退出 Service 也退出。

   ```java
   //创建启动Service的Intent
   Intent intent = new Intent(this, MusicService.class);
   //启动后台
   startService(intent);
   //关闭后台
   stopService(intent);
   ```

> 创建显式 Intents ：
>
> 1. 通过 Context、目标 Service 类创建显式 Intent
> 2. 通过 package、action 属性创建显式 Intent



### 绑定本地 Service 并与之通信

当 Service 与访问者 需要进行方法调用或交换数据时，应使用 bindService() 和 unbindService() 启动和关闭 Service。

```java
/**
* Service: 通过Intent指定需要启动的Service
* Conn: 用于监听访问者与Service之间的连接情况. 
*      访问者与Service连接成功时将回调该ServiceConnection的onServiceConnected(ComponentName name,IBinder service)方法
*      访问者与Service连接异常时将回调该ServiceConnection的onServiceDisconnected(ComponentName name,IBinder service)方法
* flags: 绑定时是否自动创建Service,参数可为0(不自动创建)或BIND_AUTO_CREATE(自动创建)
* */
public boolean bindService(Intent service, ServiceConnection conn,int flags);
```

ServiceConnection 对象的 onServiceConnected(ComponentName name,IBinder service) 有一个 IBind(Intent intent) 对象，该对象可实现与绑定 Service 之间的通信。

开发 Service 类时，该 Service 类必须提供一个 IBinder onBind(Intent intent) 方法。实际开发时通常会采用继承 Binder 的方式实现 IBinder 对象。



### 生命周期

```java
public class MusicService extends Service {
     //被创建时回调
    @Override
    public void onCreate() {
        super.onCreate();
    }
    //被绑定时回调
    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }
    //被启动时回调
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        return super.onStartCommand(intent, flags, startId);
    }
	//被取消绑定时回调
    @Override
    public boolean onUnbind(Intent intent) {
        return super.onUnbind(intent);
    }
	//被停止时回调
    @Override
    public void onDestroy() {
        super.onDestroy();
    }
}
```



### IntentService

Service 与它所在的应用位于同一进程中，因此不应该在 Service 中直接处理耗时操作。

IntentService 具有如下特征：

1. IntentService 会创建独立线程处理 onHandleIntent() 方法实现的代码，开发者无需处理多线程问题。
2. 当所有请求处理完成后，IntentService 会自动停止，因此无需调用 stopSelf() 方法来停止 Service。
3. 为 Service 的 onBind() 方法提供了默认实现，默认返回为null.
4. 为 Service 的 onStartCommand() 方法提供了默认实现，该实现会自动将请求 Intent 加入队列中。

在使用扩展 IntentService 实现 Service 无须重写 onBind()、onStartCommand() 方法，只要重写 onHandleIntent() 方法即可。



## BroadcastReceiver

broadcastReceiver 本质上是一个全局监听器，用于监听系统全局的广播消息。

### 启动步骤

1. 创建需启动的 BroadcastReceiver 的 Intent

2. 调用 Context 的 sendBroadcast() 或 sendOrderBroadcast() 方法启动指定的 BroadcastReceiver

   ```java
   //创建Intent
   Intent intent = new Intent();
   //设置action属性
   intent.setAction("org.crazyit.action.CRAZY_BROADCAST");
   intent.setPackage("org.crazyit.broadcast");
   intent.putExtra("msg","简单的消息");
   //发送广播
   sendBroadcast(intent);
   ```

   

### 实现方式

1. 继承 BroadcastReceiver 重写 onReceive(Context context, Intent inten) 方法

2. 指定该 BroadcastReceiver 能匹配的 Intent，有以下两种方式：

   - 代码中使用 BroadcastReceiver 的 Context 的 registerReceiver(BroadcastReceiver receiver,IntentFilter filter)

   ```java
   IntentFilter filter = new IntentFilter("android.provider.Telephoy.SMS_RECEIVED");
   IncomingSMSReceiver receiver = new IncomingSMSReceiver();
   registerReceiver(receiver ,filter);
   ```

   - 在 AndroidMainfest.xml 文件中配置

   ```
   <receiver android:name=".IncomingSMSReceiver">
   	<intent-filter>
   		<action android:name = "android.provider.Telephony.SMS_RECEIVERD"/>
   	</intent-filter>
   </receiver>
   ```

   > 在实践过程中，发现 Receiver 只有放在项目主目录下才能被读取注册

