---
title: 网络编程
date: 2018-12-05
tags:
 - Java
categories:
 - Java基础
---

## ## 网络基础

网络编程目的：直接或间接地通过网络协议与其它计算机实现数据交换，进行通讯。

两个主要问题：

- 如何准确地定位网络上一台或多台主机；定位主机上的特定应用；
- 找到主机后如何进行可靠高效的数据传输。

### 网络通信要素

实现主机通信：

- 通信双方地址： IP + 端口号
- 网络通信协议： OIS 参考模型 + TCP/IP 协议

### 网络通信协议

![](http://image.xiaobailx.top/images/20220210212151.png)



## IP 和端口号

### IP 地址

- 唯一的标识 Internet 上的计算机（通信实体）
- 本地回环地址(hostAddress)：127.0.0.1 主机名(hostName)：localhost
- P地址分类方式1：IPV4 和 IPV6
  - IPV4：4个字节组成，4个0-255。大概42亿，30亿都在北美，亚洲4亿。2011年初已经用尽。以点分十进制表示，如192.168.0.1
  - IPV6：128位（16个字节），写成8个无符号整数，每个整数用四个十六进制位表示，数之间用冒号（:）分开，如3ffe:3201:1401:1280:c8ff:fe4d:db39:1984。
- P地址分类方式2：公网地址( 万维网使用)和 私有地址( 局域网使用)。192.168. 开头的就是私有址址，范围即为192.168.0.0 ~192.168.255.255，专门为组织机构内部使用
- 特点：不易记忆

### 端口号

- 标识正在计算机上运行的进程（程序）
- 不同的进程有不同的端口号
-  被规定为一个 16 位的整数 0~65535
- 端口分类：
  - 公认端口：0~1023。被预先定义的服务通信占用（如：HTTP占用端口 80，FTP占用端口21，Telnet占用端口23）
  - 注册端口：1024~49151。分配给用户进程或应用程序。（如：Tomcat占用端口8080，MySQL占用端口3306，Oracle占用端口1521等）。
  - 动态/ 私有端口：49152~65535。

端口与号与IP 地址的组合得出一个网络套： 接字：Socket

### InetAddress类

- Internet上的主机有两种方式表示地址：

  - 域名(hostName)：www.gitee.com

  - IP 地址(hostAddress)： 172.19.83.237

- InetAddress类主要表示IP地址，两个子类：Inet4Address、Inet6Address。

- InetAddress 类对象含有一个 Internet 主机地址的域名和IP地址：www.gitee.com 和 180.97.125.228。

- 域名容易记忆，当在连接网络时输入一个主机的域名后，域名服务器(DNS) 负责将域名转化成IP地址，这样才能和主机建立连接。
- InetAddress 类没有提供公共的构造器，而是提供了如下几个静态方法来获取 InetAddress 实例：

```tex
public static InetAddress getLocalHost()
public static InetAddress getByName(String host)
```

- InetAddress 提供了如下几个常用的方法：

```tex
public String getHostAddress() ：返回 IP 地址字符串（以文本表现形式）
public String getHostName() ：获取此 IP 地址的主机名
public boolean isReachable(int timeout)： ：测试是否可以达到该地址
```

```java
public static void main(String[] args) throws UnknownHostException {
        InetAddress adress = InetAddress.getByName("www.gitee.com");
        System.out.println(adress);
        //获取 InetAddress 对象所含域名
        System.out.println(adress.getHostName());
        // 获取 InetAddress 对象所含IP地址
        System.out.println(adress.getHostAddress());
        
        //获取本机域名和IP地址
        System.out.println(adress.getLocalHost());
    }
```



## TCP 网络编程

### 基于 Socket 的 TCP 编程

Java语言的基于套接字编程分为服务端编程和客户端编程，其通信模型如图所示：

![](http://image.xiaobailx.top/images/20220210221007.png)



客户端Socket 的工作过程包含以下四个基本的步骤：

```java
/**
 * 1.创建创建Socket
 * <p>根据指定服务端的IP地址或端口号构造Socket类对象</p>
 **/
Socket s = new
Socket("127.0.0.1",8888);
/**
 * 2.打开连接到Socket的输入/出流
 * <p>使用 getInputStream()方法获得输入流，使用getOutputStream()方法获得输出流，进行数据传输</p>
 **/
OutputStream out = s.getOutputStream();
/**
 * 3.按照一定的协议对 Socket 进行读/写操作
 * <p>通过输入流读取服务器放入线路的信息（但不能读取自己放入线路的信息），通过输出流将信息写入线程。</p>
 **/
out.write(" hello".getBytes());
/**
 * 4. 关闭 Socket
 * <p> ：断开客户端到服务器的连接，释放线路</p>
 **/
s.close();
```



服务器程序的工作过程包含以下四个基本的步骤：

```java
/**
 * 1.调用 ServerSocket(int port)
 * <p>创建一个服务器端套接字，并绑定到指定端口上。用于监听客户端的请求。</p>
 **/
ServerSocket serverSocket = new ServerSocket(8888);
/**
 * 2.调用 accept()
 * <p>监听连接请求，如果客户端请求连接，则接受连接，返回通信套接字对象。</p>
 **/
Socket socket = serverSocket.accept ();
/**
 * 2.调用该Socket类对象的getOutputStream()和getInputStream ()
 * <p>获取输出流和输入流，开始网络数据的发送和接收</p>
 **/
InputStream in = socket.getInputStream();
byte[] buf = new byte[1024];
int num = in.read(buf);
String str = new String(buf,0,num);
System.out.println(socket.getInetAddress().toString()+":"+str);
/**
 * 4.关闭ServerSocket 和Socket 对象
 * <p>客户端访问结束，关闭通信套接字</p>
 **/
socket.close();
serverSocket.close();
```



## UDP 网络编程

- 类 DatagramSocket 和 DatagramPacket 实现了基于 UDP 协议网络程序。
- UDP数据报通过数据报套接字 DatagramSocket 发送和接收，系统不保证 UDP 数据报一定能够安全送到目的地，也不能确定什么时候可以抵达。
- DatagramPacket 对象封装了UDP数据报，在数据报中包含了发送端的 IP 地址和端口号以及接收端的 IP 地址和端口号。
- UDP协议中每个数据报都给出了完整的地址信息，因此无须建立发送方和接收方的连接。如同发快递包裹一样。

### DatagranSocket 类常用方法

- public DatagramSocket(int port)：创建数据报套接字并将其绑定到本地主机上的指定端口。套接字将被绑定到通配符地址，IP 地址由内核来选择。
- public DatagramSocket(int port,InetAddress laddr)：创建数据报套接字，将其绑定到指定的本地地址。本地端口必须在 0 到 65535 之间（包括两者）。如果 IP 地址为 0.0.0.0，套接字将被绑定到通配符地址，IP 地址由内核选择。
- public void close()：关闭此数据报套接字。
- public void send(DatagramPacket p)：从此套接字发送数据报包。DatagramPacket 包含的信息指示：将要发送的数据、其长度、远程主机的IP 地址和远程主机的端口号。
- public void receive(DatagramPacket p)：从此套接字接收数据报包。当此方法返回时，DatagramPacket 的缓冲区填充了接收的数据。数据报包也包含发送方的 IP 地址和发送方机器上的端口号。 此方法在接收到数据报前一直阻塞。数据报包对象的 length 字段包含所接收信息的长度。如果信息比包的长度长，该信息将被截短。
- public InetAddress getLocalAddress()：获取套接字绑定的本地地址。
- public int getLocalPort()：返回此套接字绑定的本地主机上的端口号。
- public InetAddress getInetAddress()：返回此套接字连接的地址。如果套接字未连接，则返回 null。
- public int getPort()：返回此套接字的端口。如果套接字未连接，则返回 -1。

### DatagramPacket 类常用方法

- public DatagramPacket(byte[] buf,int length)：构造 DatagramPacket，用来接收长度为 length 的数据包。 length 参数必须小于等于 buf.length。
- public DatagramPacket(byte[] buf,int length,InetAddress address,int port)：构造数据报包，用来将长度为 length 的包发送到指定主机上的指定端口号。length 参数必须小于等于 buf.length。
- public InetAddress getAddress()返回某台机器的 IP 地址，此数据报将要发往该机器或者是从该机器接收到的。
- public int getPort()返回某台远程主机的端口号，此数据报将要发往该主机或者是从该主机接收到的。
- public byte[] getData()返回数据缓冲区。接收到的或将要发送的数据从缓冲区中的偏移量 offset 处开始，持续 length 长度。
- public int getLength()返回将要发送或接收到的数据的长度。

UDP 网络通信

```java
//发送端
@Test
public void sender() throws IOException{
    //1.创建DatagramSocket对象
    DatagramSocket socket = new DatagramSocket();
    //2.建立发送端
    byte[] bytes = "hello,server".getBytes();
    InetAddress inetAddr = InetAddress.getLocalHost();
    //3.建立数据包
    DatagramPacket packet = new DatagramPacket(bytes, 0, bytes.length,inetAddr, 9090);
    //4.调用Socket的发送方法
    socket.send(packet);
    //5.关闭Socket
    if (socket != null) {
        socket.close();
    }
}

//接收端
@Test
public void receiver() throws IOException {
    //1.创建DatagramSocket对象
    DatagramSocket socket = new DatagramSocket(9090);
    //2.建立接收端
    byte[] bytes = new byte[1024];
    //3.建立数据包
    DatagramPacket packet = new DatagramPacket(bytes,bytes.length);
    //4.调用Socket的接收方法
    socket.receive(packet);
    String str = new String(packet.getData(), 0, packet.getLength());
    System.out.println(str + "==>" + packet.getAddress());
    //5.关闭Socket
    if (socket != null) {
        socket.close();
    }
}
```



## URL 编程

### URL类

URL(Uniform Resource Locator)：统一资源定位符，它表示 Internet 上某一资源的地址。

URL的基本结构由5部分组成：```< 传输协议>://< 主机名>:< 端口号>/< 文件名># 片段名?参数列表``` 



### URL 类构造器

```tex
//public URL (String spec)：通过一个表示URL地址的字符串可以构造一个URL对象。
URL url = new URL ("http://www. atguigu.com/");

//public URL(URL context, String spec)：通过基URL和相对URL构造一个URL对象。
URL downloadUrl = new URL(url, "download. html");

//public URL(String protocol, String host, String file)
new URL("http","www.baidu.com", "download. html");

//public URL(String protocol, String host, int port, String file)
URL gamelan = new URL("http", "www.baidu.com", 80, "download. html");
```



### URL 类常用方法

```tex
public String getProtocol( ) 获取该URL的协议名
public String getHost( ) 获取该URL的主机名
public String getPort( ) 获取该URL的端口号
public String getPath( ) 获取该URL的文件路径
public String getFile( ) 获取该URL的文件名
public String getQuery( ) 获取该URL的查询名
```



### URLConnection类

```java
@Test
public void loadIndex() throws IOException {
    URL downloadUrl = new URL("https://www.baidu.com/index.html");

    HttpURLConnection connection = (HttpURLConnection) downloadUrl.openConnection();
    connection.connect();
    InputStream inputStream = connection.getInputStream();
    FileOutputStream fileOutputStream = new FileOutputStream("index.html");
    byte[] buffer = new byte[1024];
    int len;
    while ((len = inputStream.read(buffer))!=-1){
        fileOutputStream.write(buffer,0,len);
    }
    if (fileOutputStream!=null){
        fileOutputStream.close();
    }
    if (inputStream!=null){
        inputStream.close();
    }
    if (connection!=null){
        connection.disconnect();
    }
}
```

