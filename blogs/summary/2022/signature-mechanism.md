---
title: Android签名机制详解
date: 2022-08-19
tags:
 - Android
categories:
 - 学习总结
---

近期由于工作需要在学习 Android 的签名机制，因为没有现成资料，只能通过开发者文档和阅读博客的方式对 Android 签名机制进行大致了解。过程中查阅到的资料相对零散，不够系统和全面，对于刚入门 Android 学习的小白来说，要快速掌握其内容着实是一大挑战。本文建立与各位前辈的基础之上，加之自己在学习过程中的理解，对 Android 签名机制所涉及的内容进行一个系统梳理，一个是对自己学习过程的一个复盘，二也希望能够对刚入门的同学有所启发和帮助。



## 签名机制

签名机制本质是对信息的一种加密措施，为的是保证信息的合法性。在说明签名机制之前我们先来了解其中涉及到的四个概念：数字摘要、非对称加密、数字签名、数字证书。

- **数字摘要：** 采用单向 Hash 函数将需要加密的明文生成一串固定长度的密文，这一串密文又称为数字指纹，其具有唯一性和不可逆性。数字摘要的生成取决于 Hash 算法，常用的 Hash 算法有 MD5 、SHA1、SHA256，MD5 的长度是128位，SHA1的长度是160位，SHA256的长度为256位。

- **非对称加密：** 非对称加密即加密和解密的密钥不同，其中涉及到一对密钥：公钥和私钥。当一消息通过公钥加密。解密只能通过其对应的私钥完成；当消息通过私钥加密，只能通过其对应的公钥解密。


![](http://image.xiaobailx.top/images/202208162221419.png)

- **数字签名：** 通过公钥对明文进行加密得到的一段数字串，用于保证信息来源的真实性和消息的完整性。

- **数字证书：** 校验过程有个前提：接收方必须要知道发送方的公钥和所使用的算法。如果数字签名和公钥一起被篡改，接收方无法得知，还是会校验通过。如何保证公钥的可靠性呢？答案是数字证书，数字证书是身份认证机构（Certificate Authority）颁发的，包含了以下信息：

  - 证书颁发机构
  - 证书颁发机构签名
  - 证书绑定的服务器域名
  - 证书版本、有效期
  - 签名使用的加密算法（非对称算法，如RSA）、公钥 等

  接收方收到消息后，先向CA验证证书的合法性（根据证书的签名、绑定的域名等信息。CA机构是权威的，可以保证这个过程的可靠性）再进行签名校验。


了解了以上概念，我们来看一下签名的过程：

![](http://image.xiaobailx.top/images/202208162221367.png)

签名过程就是 **使用非对称加密算法用私钥对摘要进行加密** 的过程。我们再看一下签名的校验过程：

![](http://image.xiaobailx.top/images/202208162221390.png)

校验过程与签名过程相反，消息接受者 **通过数字证书中的公钥信息和非对称算法对签名进行解密，然后用相同的 Hash 算法对明文进行摘要计算，再将计算出的摘要与解密后得到的摘要进行对比，以校验消息的合法性。**



## Android签名机制

Android 签名与传统签名机制有些许不同。

首先，APK 的数字证书没有通过 CA 机构申请，而是开发者自定义的，且 Android 在安装 APK 时并不会校验证书本身的合法性，只是从中提取公钥和加密算法。正因如此，第三方 APK 在重新签名后依然能在没有安装这个 APK 系统中继续安装。

此外，Android 在对 APK 签名时并没有直接指定私钥、公钥和数字证书，而是使用 keystore 文件，将这些信息都包含在 keystore 文件中。



### keystore

根据编码格式不同，keystore 文件分为很多种，Android 使用的是 Java 标准 keystore 格式 JKS (Java Key Storage)，所以通过 Android Studio 导出的 keystore 文件是以 .jks 结尾的。

keystore 使用的证书标准是 X.509，X.509 标准也有多种编码格式，常用的有两种：pem（Privacy Enhanced Mail）和 der（Distinguished Encoding Rules）。jks 使用的是 der 格式，Android 也支持直接使用 pem 格式的证书进行签名。

两种证书编码格式的区别：

- DER（Distinguished Encoding Rules）：二进制格式，所有类型的证书和私钥都可以存储为der格式；

- PEM（Privacy Enhanced Mail）：base64编码，内容以—–BEGIN xxx—– 开头，以—–END xxx—– 结尾。



### 密钥库生成

keytool 是 Java 自带的证书生成工具，位于 JAVA_HOME 的 bin 目录下安装目录下，以下是使用 keytool 生成密钥库的简单操作。

1. 使用 JDK 自带的 keytool 工具，执行以下指令

```shell
keytool -genkeypair -keystore <keystore-name> -alias <alis> -validity <period-of-validity> -keyalg RSA
#例：生成密钥库test-keystore.jks，其有效期为365天
keytool -genkeypair -keystore test-keystore.jks -alias mytest -validity 365
#test-keystore.jks：密钥库名 
#mytest：密钥别名,作用在于当密钥库可以存在多个密钥对，可以区分不同密钥对，算法只支持RAS和DSA
#android：密钥库密码
```

> 默认情况下直接通过Android Studio在Run或Debug时，会使用主目录下的默认密钥库 .android\debug.keystore 对 App 签名
>

2. 创建完毕后，可以查看密钥对信息

```shell
keytool -v -list -keystore <keystore-name>
#例：以上条命令生成密钥库test-keystore.jks文件为例
keytool -v -list -keystore test-keystore.jks
```

keytool  的几个常用命令我也在这做了一个简单整理，如下

```shell
#生成密钥对
keytool -genkeypair -keystore <keystor-name> -alias <alis> -validity <period-of-validity> -keyalg RSA
#查看密钥库详情
keytool -v -list -keystore <keystore-name>
#查看密钥库证书
keytool -list -rfc -keystore <keystore-name> -storepass <keystore-password>
#导出公钥
keytool -list -rfc --keystore <keystore-name> | openssl x509 -inform pem -pubkey
#v1签名校验
keytool -printcert -jarfile xxxx.apk
```

关于 keytool 的详细使用教程，可以看一下这篇文章：[使用JDK自带keytool生成证书](https://blog.csdn.net/kunlyy/article/details/105399255)



## Android签名方案

截至目前，Android 已推出 4 种签名方案，即 V1、V2、V3、V3。总体上我们可以将其归类为 V1 和 V2+，V2 版本之后的设计原理与 V2 基本保持一致，因此我们重点需要理解 V2 签名的设计原理。那么接下来，我们将一步步探究 Android 签名机制的实现原理。



### 签名方案—V1

了解 V1 签名原理之前，我们先来看一下 APK 的目录结构。找到任意一个已经进行签名的 APK 通过解压软件打开，打开后我们可以看到一个 MATE-INF 目录，点击进入，我们可以看到 MANIFEST.MF、*.SF 和 *.RSA 三个文件，如下图所示：

![image-20220815161542947](http://image.xiaobailx.top/images/202208162221535.png)

接下来，我们来新认识一个新的工具— jarsigner，其位于 JAVA_HOME/bin/jarsigner，它是 Android 早期用于对 APK 进行签名的工具。找一个 APK 包，删除其 MATE-INF 目录，执行以下命令。

```shell
#对xxx.apk进行签名
jarsigner -keystore <keystore-name> xxx.apk <alias>
#兼容android 4.1以下，若密钥库中有多个密钥对，则必须指定密钥别名。
jarsigner -keystore <keystore-name> -digestalg SHA1 -sigalg SHA1withRSA xxx.apk <alias>
#例:使用test-keystore.jks对test.apk进行签名
jarsigner -keystore test-keystore.jks test.apk test-signed.apk
```

可以发现，APK 包下重新生成了 MATE_INF 目录，同时 MATE_INF 目录下依然保存了 MANIFEST.MF、*.SF 和 *.RSA  三个文件。我们可以分别查看一下这三个文件内容：

- MAINFEST.MF内容

![image-20220815165340924](http://image.xiaobailx.top/images/202208162221680.png)

- *.SF 内容

![image-20220815165527958](http://image.xiaobailx.top/images/202208162221067.png)

- *.RSA 是二进制文件，无法直接查看，我们使用 keytool 在命令行窗口进行查看：

```shell
  keytool -printcert -file *.RSA
```

![](http://image.xiaobailx.top/images/202208162221242.png)

通过以上观察，我们以上三个文件的结构有一个大体印象。接下来，我们来正式认识这几个文件。

- MANIFEST.MF：保存了 APK 除 META-INF 目录外的所有文件对应的数字摘要信息。在签名过程中，freamwork 首先会遍历 APK 包下的所有文件(entry)，然后逐个生成 SHA256（JDK7.0之前采用 SHA1算法）数字摘要信息，再用 base64 进行编码之后将生成的摘要写入 MANIFEST.MF 文件中。

- *.SF：对前一步生成的 MANIFEST.MF，使用 SHA256-RSA（JDK7.0之前采用 SHA1算法） 算法，用私钥进行签名，其中的值是对清单文件里的 SHA256 再进行 SHA256 后再次 base64 得到。

  > RSA 是一种非对称加密算法。用私钥通过 RSA 算法对摘要信息进行加密。在安装时只能使用公钥才能解密它。解密之后，将它与未加密的摘要信息进行对比，如果相符，则表明内容没有被异常修改。

- *.RAS：生成 MANIFEST.MF 没有使用密钥信息，而生成 *.SF 文件使用了私钥文件。*.RSA 文件中保存了公钥、所采用的加密算法等信息 ，framework 会解析这个 RAS 文件。

![](http://image.xiaobailx.top/images/202208162221390.png)

> 对中间过程中的签名校验，可以参考这篇博客：[ APK签名机制之——JAR签名机制详解](https://blog.csdn.net/zwjemperor/article/details/80877305)

在校验过程，freamwork 首先会读取 *.RSA 中的公钥信息和加密算法信息，然后对 *.SF 文件的签名逐一解析，再与签名过程中相同的 Hash 算法计算出 APK 中非 MATE_INFO 目录下的各个文件的摘要，并与签名解析出的对应摘要对比，以此确定 APK 的完整性。

通过以上分析，我们对 V1 签名有了一个较为基础的认识和理解。V1 是基于 jar 的签名方式，该方案两个明显的缺陷：

1. 校验效率低，遍历每个文件需要进行 IO 操作，对于一个庞大的项目来说效率非常不可观；
2. 安全性低，由于校验时不对 META-INF 目录下的文件进行校验，因此存在一定的安全隐患。

> 关于 JAR 签名文件的说明，可以看一下官网介绍：[JAR 文件规范 (oracle.com)](https://docs.oracle.com/javase/8/docs/technotes/guides/jar/jar.html#Signed_JAR_File)



### 签名方案—V2

由于 V1 签名方案在校验 APK 完整性上并不完善，且存在效率问题，谷歌在 Android 7.0 引入了 V2 签名方案。V2 的设计原理与 V1 相较也发生了根本性变化。在了解 V2 的签名原理之前，我们先来认识一下 ZIP 文件结构，这对我们理解 V2 签名的工作流程及其重要。

#### 认识ZIP文件

ZIP 文件结构如下：

![](http://image.xiaobailx.top/images/202208162221294.png)

ZIP 文件结构包括三大部分：**数据区、中央目录和中央结尾记录**，数据区包含了所有的文件记录，中央目录存储了所有本地文件头信息，中央目录结尾存放了中央目录的相关信息。

在解析 ZIP 文件时，程序首先会找到 ZIP 文件的 **中央目录结尾**，然后在中央目录结尾中找到 **核心目录的起始偏移量** 定位到 **中央目录起始位置**，然后开始遍历中央目录里的 **本地文件头** ，根据本地文件头中的 **文件头的相对位移** 定位到相应的本地文件。

> 关于 ZIP 文件结构解析过程的具体细节，可以参考这篇文章：[Zip文件格式详解](https://blog.csdn.net/qq_43278826/article/details/118436116)

无论是 jar 包还是 APK 包，其本质都是 zip 文件，因此我们在解析 jar 和 APK 时就是在解析 zip。而 V2 的实现原理就是对 ZIP 文件进行一定处理。



#### V2 方案解析

V2 是一种全文件签名方案，在使用 V2 方案对 APK 进行签名时，会在 APK 文件中插入一个 **APK 签名分块**，该分块位于 **ZIP 中央目录** 部分之前并紧邻该部分。在**APK 签名分块** 内，v2 签名和签名者身份信息会存储在 APK 签名方案 v2 分块中。

![image-20220816113002000](http://image.xiaobailx.top/images/202208162221426.png)



##### APK 签名分块

![](http://image.xiaobailx.top/images/202208162222685.png)在解析 APK 文件时，首先要通过以下方法找到 **ZIP 中央目录** 的起始位置：在文件末尾找到 **ZIP 中央目录结尾** 记录，然后从该记录中读取 **中央目录的起始偏移量**  。通过 **magic** 值，可以快速确定中央目录前方可能是 **APK 签名分块**。然后，通过 **size of block** 值，可以高效地找到该分块在文件中的起始位置。

在解译该分块时，程序将忽略 ID 未知的ID-值对。



##### APK 签名方案 V2 分块

APK 由一个或多个签名者/身份签名，每个签名者/身份均由一个签名密钥来表示。该信息会以APK 签名方案 v2 分块的形式存储。对于每个签名者，都会存储以下信息：

- （签名算法、摘要、签名）元组。摘要会存储起来，以便将签名验证和 APK 内容完整性检查拆开进行。
- 表示签名者身份的 X.509 证书链。
- 采用键值对形式的其他属性。

![](http://image.xiaobailx.top/images/202208162222771.png)

**APK 签名方案 v2 分块** 存储在 **APK 签名分块** 内，ID 为  **0x7109871a**。APK 签名方案 v2 分的格式如下

![](http://image.xiaobailx.top/images/202208162222776.png)

##### APK 完整性保护

签名后的 APK 文件包含以下四个部分：

![签名后的各个 APK 部分](https://source.android.google.cn/security/images/apk-sections.png)

APK 签名方案 v2 负责保护第 1、3、4 部分的完整性，以及第 2 部分包含的 **APK 签名方案 v2 分块** 中的 **signed data 分块** 的完整性。

第 1、3 和 4 部分的完整性通过其内容的一个或多个摘要来保护，这些摘要存储在 **signed data** 分块中，而 **这些分块则通过一个或多个签名来保护** 。

第 1、3 和 4 部分的摘要采用以下计算方式，类似于两级 [Merkle 树](https://en.wikipedia.org/wiki/Merkle_tree)。每个部分都会被拆分成多个大小为 1MB（220 个字节）的连续块。每个部分的最后一个块可能会短一些。每个块的摘要均通过 **字节 0xa5 的串联、块的长度（采用小端字节序的 uint32 值，以字节数计）和块的内容** 进行计算。顶级摘要通过  **字节0x5a 的串联、块数（采用小端字节序的 uint32 值）以及块的摘要的连接（按照块在 APK 中显示的顺序）** 进行计算。摘要以分块方式计算，以便通过并行处理来加快计算速度。

![APK 摘要](https://source.android.google.cn/security/images/apk-integrity-protection.png)

由于第 4 部分（ZIP 中央目录结尾）包含 ZIP 中央目录的偏移量，因此该部分的保护比较复杂。当 **APK 签名分块** 的大小发生变化（例如，添加了新签名）时，偏移量也会随之改变。因此，在通过 **ZIP 中央目录结尾** 计算摘要时，必须将包含 **ZIP 中央目录** 偏移量的字段视为包含 **APK 签名分块** 的偏移量。

##### 签名验证

在 Android 7.0 及更高版本中，可以根据 APK 签名方案 v2+ 或 JAR 签名（v1 方案）验证 APK。更低版本的平台会忽略 v2 签名，仅验证 v1 签名。APK 签名验证过程如下图所示。（新步骤以红色显示）

![APK 签名验证过程](https://source.android.google.cn/security/images/apk-v2-validation.png)

1. 找到 APK 签名分块并验证以下内容：

   1. APK 签名分块的两个大小字段包含相同的值。
   2. ZIP 中央目录结尾紧跟在ZIP 中央目录记录后面。
   3. ZIP 中央目录结尾之后没有任何数据。
2. 找到 APK 签名分块中的第一个 APK 签名方案 v2 分块。如果 v2 分块存在，则继续执行第 3 步。否则，回退至使用 v1 方案验证 APK。

3. 对 APK 签名方案 v2 分块中的每个 signer，执行以下操作：

   1. 从 **signatures** 中选择安全系数最高的受支持 **signature algorithm ID**。安全系数排序取决于各个实现/平台版本。
2. 使用 **public key** 并对照 **signed data** 验证 **signatures** 中对应的 **signature**。（现在可以安全地解析 **signed data** 了）
   3. 验证 **digests** 和 **signatures** 中的签名算法 ID 列表（有序列表）是否相同。（这是为了防止删除/添加签名）
   4. 使用签名算法所用的同一种摘要算法计算 APK 内容的摘要。
   5. 验证计算出的摘要是否与 **digests** 中对应的 **digest** 一致。
6. 验证 **certificates** 中第一个 **certificate** 的 SubjectPublicKeyInfo 是否与 **public key** 相同。
   
4. 如果找到了至少一个 **signer**，并且对于每个找到的 **signer**，第 3 步都取得了成功，APK 验证将会成功。

> 注意：如果第 3 步或第 4 步失败了，则不得使用 v1 方案验证 APK。

对于以上校验过程，我们可以大致归纳为校验校验 APK 文件格式、找 V2 签名分块、对 V2 签名方案的 signer 分块的签名数据进行校验。其中在对 signer 分块的签名校验过程相对于其他几个过程涉及到更多专业术语，不容易看懂，我们把这个过程用图解方式进行解释。

![](http://image.xiaobailx.top/images/202208162223192.png)

上述内容基本来自开发者文档：[Andorid 开发者文档—APK 签名方案 v2](https://source.android.google.cn/security/apksigning/v2)，另外，[V2签名机制详解](https://blog.csdn.net/zwjemperor/article/details/81051120) 这篇文章也对我有很大的启发。



#### apksigner

apksigner 是 Google 官方提供的针对 Android APK 签名及验证的专用工具， 该工具位于 Android SDK/build-tools/SDK 版本 /apksigner.bat。

```shell
apksigner sign --ks <keystore-name> --ks-key-alias <alias> xxx.apk
#禁用v2,若密钥库中有多个密钥对,则必须指定密钥别名
apksigner sign --v2-signing-enabled false --v1-signing-enabled true --ks <keystore-name> xxx.apk
#例：禁用V1签名,使用V2对test.apk进行签名
apksigner sign --v1-signing-enabled false --ks debug.keystore --ks-key-alias debugkey test.apk
```

apksigner 除了支持使用 keystore 文件进行签名外，还支持直接指定 pem 证书文件和私钥进行签名。

关于 apksigner 的详细使用，可以参考开发者手册：https://developer.android.google.cn/studio/command-line/apksigner



### 签名方案—V3

在上一章中，我们重点了解了 ZIP 文件的结构以及 V2 签名原理，基于对 V2 签名方案的认识，我们再来看一下 V3 签名方案。

#### APK 签名方案 V3 分块

V3 签名方案时 Android 9.0 时引入的签名解决方案，支持了密钥轮替。根据官网介绍，V3 签名方案与 V2 基本相似，只是在 APK 签名分块中添加了有关受支持的 SDK 版本和 proof-of-rotation 结构的信息，应用可以通过将 APK 文件过去的签名证书链接到现在签署应用时使用的证书，从而使用新签名证书来签署应用。我们与 V2 对比来看一下 APK 签名块 V3 的结构， 橙色部分为新增的结构信息。 

![](G:\工作笔记\markdown\2208191606.png)



签名数据部分中的 proof-of-rotation 属性包含一个单链表，其中每个节点都包含用于为之前版本的应用签名的签名证书。该单链表按版本排序，最旧的签名证书对应于根节点。在构建 proof-of-rotation 数据结构时，系统会让每个节点中的证书为列表中的下一个证书签名，从而为每个新密钥提供证据来证明它应该与旧密钥一样可信。



#### 签名校验

V3 的校验过程也与 V2 大致相同，只是 V3 在 V2 校验的基础上添加了对 SDK 版本校验和 proof-of-rotation 结构的校验，其校验过程如下：

![](G:\工作笔记\markdown\2208191605.png)

>  开发者文档说明：[APK 签名方案 v3](https://source.android.google.cn/security/apksigning/v3)



### 签名方案—V4

> 在传统的应用安装方案中，开发者通过 ADB（Android Debug Bridge）以有线或无线的方式与终端用户连接，或者用户从软件商店直接下载，然而该方案需要用户等待完整的安装包传输结束后才能启动安装，在这期间产生了不良的用户体验。
>
> 增量安装技术是一种流式的安装方案：一旦安装包的核心文件传输完成便可启动应用。流式安装意味着允许优先传输核心数据以启动应用，并在后台流式传输剩余数据。
>
> 在Android 11中，Google在内核中实现了增量文件系统用于对增量安装的支持。(详见 https://source.android.com/devices/architecture/kernel/incfs）
>
> 这使得 Android os 可以通过 ADB 流式传输 APK。同时，Android 11 为了适应增量安装，添加了新的 v4签名方案。
>
> 此方案不改变前代签名方案而是创建一种新的签名：基于 APK 所有字节数据计算出 Merkle 哈希树，并将Merkle 树的根哈希、盐值作为签名数据进行包完整性验证。新的签名数据保存在 .idsig 文件中并且在进行增量安装前必须为APK创建对应的 v4 签名文件。

Android 11 的

签名方案 V4 是 Android 11 的签名方案，Android 11 在内核中实现了增量文件系统用于对增量安装的支持，关于增量安装可以参考这篇文章：[Android AAB增量安装](https://blog.csdn.net/weixin_42600398/article/details/123034521)。实现了 支持了流式传输。V4 签名基于根据 APK 的所有字节计算得出的 Merkle 哈希树。将Merkle 树的根哈希、盐值作为签名数据进行包完整性验证。其签名信息存储在单独的 `<apk name>.apk.idsig` 文件中并且在进行增量安装前必须为APK创建对应的 v4 签名文件。V4 签名需要 [V2](https://source.android.google.cn/security/apksigning/v2) 或 [V3](https://source.android.google.cn/security/apksigning/v3) 签名作为补充。

关于 V4 签名的内容，可以直接参考开发者文档说明：[APK 签名方案 v4](https://source.android.google.cn/security/apksigning/v4)，在此不再赘述。



以上便是我对学习 Android 签名校验过程的一些内容整理和理解。当这些内容梳理完后，再看相关源码，顿觉豁然开朗！



参考博客：

1. [APK签名详解及Android Studio 在线调试系统App](https://blog.csdn.net/CrazyMo_/article/details/107742963)
2. [APK签名机制原理详解](https://blog.csdn.net/zwjemperor/article/details/80877203)
3. [Zip文件格式详解](https://blog.csdn.net/qq_43278826/article/details/118436116)
4. [V2签名机制详解](https://blog.csdn.net/zwjemperor/article/details/81051120)
5. [apksigner 的使用](https://developer.android.google.cn/studio/command-line/apksigner#usage-sign)
6. [细说Android apk四代签名：V1、V2、V3、V4](https://blog.csdn.net/chzphoenix/article/details/118180869)
7. [AndroidV1,V2,V3签名原理详解](https://blog.csdn.net/qq_45272690/article/details/120576341)
8. [Android V2签名与校验原理分析](https://blog.csdn.net/qq_43278826/article/details/119237062)
9. [Android AAB增量安装](https://blog.csdn.net/weixin_42600398/article/details/123034521)
10. [APK 签名方案 v2](https://source.android.google.cn/security/apksigning/v2)
11. [APK 签名方案 v3](https://source.android.google.cn/security/apksigning/v3)



