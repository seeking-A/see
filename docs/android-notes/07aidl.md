# AIDL学习笔记

## 简介

AIDL 全称为 Android Interface Definition Language（Android接口定义语言），其目的是为了**实现进程间通信，尤其是在涉及多进程并发情况下的进程间通信**。



## 语法

文件类型：后缀是 .aidl，而不是 .java。

数据类型：AIDL默认支持一些数据类型，在使用这些数据类型的时候是不需要导包的，但是除了这些类型之外的数据类型，在使用之前必须导包。

默认支持的数据类型包括：包括 byte，short，int，long，float，double，boolean，char，String，CharSequence，List，Map。

> List类型：List中的所有元素必须是AIDL支持的类型之一，或者是一个其他AIDL生成的接口，或者是定义的parcelable，可以使用泛型。
>
> Map类型：Map中的所有元素必须是AIDL支持的类型之一，或者是一个其他AIDL生成的接口，或者是定义的parcelable。Map是不支持泛型的。

定向tag：AIDL中的定向 tag 表示了在跨进程通信中数据的流向，其中 in 表示数据只能由客户端流向服务端， out 表示数据只能由服务端流向客户端，而 inout 则表示数据可在服务端与客户端之间双向流通。其中，数据流向是针对在客户端中的那个传入方法的对象而言的。in 为定向 tag 的话表现为服务端将会接收到一个那个对象的完整数据，但是客户端的那个对象不会因为服务端对传参的修改而发生变动；out 的话表现为服务端将会接收到那个对象的的空对象，但是在服务端对接收到的空对象有任何修改之后客户端将会同步变动；inout 为定向 tag 的情况下，服务端将会接收到客户端传来对象的完整信息，并且客户端将会同步服务端对该对象的任何变动。



## 使用步骤

1. 使数据类实现 Parcelable 接口

> 注：若AIDL文件中涉及到的所有数据类型均为默认支持的数据类型，则无此步骤。因为默认支持的那些数据类型都是可序列化的。

2. 书写AIDL文件生成 aidl 目录；
3. 移植相关文件，保证在客户端和服务端中都有需要用到的 .aidl 文件和其中涉及到的 .java 文件；
4. 编写服务端代码，实现 Service 子类的 onbind() 方法，在该方法中实现 .aidl 文件中定义的接口；
5. 在 Manefest 文件里面注册这个我们写好的 Service
6. 在客户端通过 bindService() 方法连接服务端，调用服务端的方法进行通信。



## 源码分析

### 客户端

从客户端开始

```java
public void onServiceConnected(ComponentName name, IBinder service) 
    aidlInterface = AIDLInfterface.Stub.asInterface(service);
}
```

查看 AIDLInfterface.Stub.asInterface(service) 的实现

```java
public static com.xxx.xxx.AIDLInfterface asInterface(android.os.IBinder obj) {
    //验空
    if ((obj == null)) {
        return null;
    }
    //DESCRIPTOR = "com.xxx.xxx.AIDLInfterface"，搜索本地是否已经
    //有可用的对象了，如果有就将其返回
    android.os.IInterface iin = obj.queryLocalInterface(DESCRIPTOR);
    if (((iin != null) && (iin instanceof com.xxx.xxx.ParcelableManager))) {
        return ((com.xxx.xxx.ParcelableManager) iin);
    }
    //如果本地没有的话就新建一个返回
    return new com.xxx.xxx.AIDLInfterface.Stub.Proxy(obj);
}
```

跟踪实现类

```java
private static class Proxy implements com.xxx.xxx.AIDLInfterface {
    private android.os.IBinder mRemote;
    Proxy(android.os.IBinder remote) {
        //此处的 remote 正是前面我们提到的 IBinder service
        mRemote = remote;
    }
    @Override
    public java.util.List<com.xxx.xxx.Parcelable> getParcelables() throws android.os.RemoteException {
        //省略
    }
}
```

看具体接口实现

```java
@Override
public java.util.List<com.xxx.xxx.Parcelable> getParcelables() throws android.os.RemoteException {
    //很容易可以分析出来，_data用来存储流向服务端的数据流，
    //_reply用来存储服务端流回客户端的数据流
    android.os.Parcel _data = android.os.Parcel.obtain();
    android.os.Parcel _reply = android.os.Parcel.obtain();
    java.util.List<com.xxx.xxx.Parcelable> _result;
    try {
        _data.writeInterfaceToken(DESCRIPTOR);
        //调用 transact() 方法将方法id和两个 Parcel 容器传过去
        mRemote.transact(Stub.TRANSACTION_getParcelables, _data, _reply, 0);
        _reply.readException();
        //从_reply中取出服务端执行方法的结果
        _result = _reply.createTypedArrayList(com.xxx.xxx.Parcelable.CREATOR);
    } finally {
        _reply.recycle();
        _data.recycle();
    }
    //将结果返回
    return _result;
}
```

总结一下在 Proxy 类的方法里面一般的工作流程：

1，生成 _data 和 _reply 数据流，并向 _data 中存入客户端的数据。
2，通过 transact() 方法将它们传递给服务端，并请求服务端调用指定方法。
3，接收 _reply 数据流，并从中取出服务端传回来的数据。



### 服务端

客户端通过调用 transact() 方法将数据和请求发送过去，服务端应当有一个方法来接收这些传过来的东西，轻易的找到一个叫做 onTransact() 的方法

```java
@Override
public boolean onTransact(int code, android.os.Parcel data, android.os.Parcel reply, int flags) throws android.os.RemoteException {
    switch (code) {
        case INTERFACE_TRANSACTION: {
            reply.writeString(DESCRIPTOR);
            return true;
        }
        case TRANSACTION_getParcelables: {
            //省略
            return true;
        }
    }
    return super.onTransact(code, data, reply, flags);
}
```

看具体实现

```java
case TRANSACTION_getParcelables: {
    data.enforceInterface(DESCRIPTOR);
    //调用 this.getParcelables() 方法，在这里开始执行具体的事务逻辑
    //result 列表为调用 getParcelables() 方法的返回值
    java.util.List<com.xxx.xxx.Parcelable> _result = this.getParcelables();
    reply.writeNoException();
    //将方法执行的结果写入 reply ，
    reply.writeTypedList(_result);
    return true;
}
```

总结一下服务端的一般工作流程：

1. 获取客户端传过来的数据，根据方法 ID 执行相应操作。
2. 将传过来的数据取出来，调用本地写好的对应方法。
3. 将需要回传的数据写入 reply 流，传回客户端。



参考文章：[Android：学习AIDL，这一篇文章就够了(上)](https://blog.csdn.net/luoyanglizi/article/details/51980630)

参考文章：[Android：学习AIDL，这一篇文章就够了(下)](https://blog.csdn.net/luoyanglizi/article/details/52029091)

