---
title: Linux文件管理
date: 2021-11-22
tags:
 - Linux
categories:
 - Linux
---

## Linux 文件基础知识

### Linux 常用文件类别

1. **普通文件： **不包含有文件系统的结构信息，如图形文件，数据文件，文档文件，声音文件，文件标识为 **符号横线**

   ```shell
   # 查看 /etc 目录下的文件
   ls -l /etc
   ```

   ![image-20211122085923575](http://image.xiaobailx.top/images/20211122085930.png)

   > **分类：**
   >
   > - **文本文件：** 包含用户可读信息的文件，以ASCII码方式存储，可显示和打印。
   >
   > - **二进制文件：** 包含计算机可读信息的文件，可以是可执行的文件，使系统根据其中的指令完成某项工作。




2.  **目录文件：** 存放文件名及其相关信息文件，文件标识为 **d**

   ```shell
   # 查看 /usr 目录下的文件
   ls -l /usr
   ```

   ![image-20211122092715635](http://image.xiaobailx.top/images/20211122092715.png)



3. **链接文件：** 类似于 windows 下的快捷方式，文件标识为 **i**

   ```shell
   # 查看 /dev/initctl 目录下的链接文件
   ls -l /dev/initctl
   ```

   ![image-20211122093345495](http://image.xiaobailx.top/images/20211122093345.png)

   > **分类：**
   >
   > - 硬链接
   > - 符号链接



4. **设备文件：** 将外部设备视为一种特殊文件，存在 /dev 目录下，文件标识为 **c** 

   ```shell
   # 查看设备文件信息
   ls -l /dev
   ```

   ![image-20211122094131977](http://image.xiaobailx.top/images/20211122094132.png)



5. **管道文件：** 一种特殊文件，用于不同进程间的信息传递



### Linux 系统目录结构

**基本概念**

- 路径：一个路径可以唯一表示一个目录或者文件，多级路径直接可以使用 / 进行分割

  ```shell
  # 使用绝对路径切换目录bin
  cd /bin
  # 使用相对路径切换目录bin
  cd ../bin
  ```

  ![image-20211122094829582](http://image.xiaobailx.top/images/20211122094829.png)

  ![image-20211122095024567](C:/Users/20689/AppData/Roaming/Typora/typora-user-images/image-20211122095024567.png)

- 根目录：/ ，是所有目录的起点

  ```shell
  # 进入到根目录中
  cd /
  # 查看根目录中的内容
  ls
  ```

  ![image-20211122095231703](http://image.xiaobailx.top/images/20211122095231.png)

- 用户主目录： 每个用户都有自己的主目录，root用户的主目录是/root，其他用户主目录与用户名相同，在/home目录下
  **~** 符号表示自己的主目录

  ```shell
  # 使用 ~ 回到自己的主目录中
  cd ~
  # 查看自己主目录的位置
  pwd
  ```

  ![image-20211122095713962](http://image.xiaobailx.top/images/20211122095714.png)

- 工作目录：用户登录后所处的目录就是工作目录，**.** 表示当前目录，**..** 表示上级目录

  ```shell
  # 查看自己的当前所在的工作目录
  pwd
  # 查看 符号 . 和 ..
  ls -a /
  # 使用 .. 返回到上级目录
  cd ..
  ```

  ![image-20211122100123292](http://image.xiaobailx.top/images/20211122100123.png)



**linux系统目录及说明**

Linux 以文件目录的方式组织和管理系统中所有的文件，采用树形节后组织

![](http://image.xiaobailx.top/images/20211122110425.png)



- **/：** 根目录
- **/bin：** binaries (二进制文件) 的缩写, 存放着最经常使用的命令。
- **/boot：** 存放启动 Linux 时使用的一些核心文件，包括一些连接文件以及镜像文件。
- **/dev：** device (设备) 的缩写，存放的是 Linux 的外部设备文件，和访问文件的方式是相同的。
- **/etc：** etcetera (等等) 的缩写，存放所有的系统管理所需要的配置文件和子目录。
- **/home：** 普通用户的主目录，以用户的账号命名。
- **/lib：** library (库) 的缩写，存放着系统最基本的动态连接共享库，其作用类似于 Windows 里的 DLL 文件。
- **/media：** linux 系统会自动识别一些设备，例如U盘、光驱等等，当识别后，Linux 会把识别的设备挂载到这个目录下。
- **/mnt：** 是为了让用户临时挂载别的文件系统的，我们可以将光驱挂载在 /mnt/ 上，然后进入该目录就可以查看光驱里的内容了。
- **/opt**： optional (可选) 的缩写，这是给主机额外安装软件所摆放的目录。
- **/proc**： processes (进程) 的缩写，存储的是当前内核运行状态的一系列特殊文件，这个目录是一个虚拟的目录，它是系统内存的映射，我们可以通过直接访问这个目录来获取系统信息。
- **/root：** root用户的主目录
- **run**：是一个临时文件系统，存储系统启动以来的信息。当系统重启时，这个目录下的文件应该被删掉或清除。
- **/sbin**：是 superuser binaries (超级用户的二进制文件) 的缩写，这里存放的是系统管理员使用的系统管理程序。
- **/srv**：该目录存放一些服务启动之后需要提取的数据。
- **/sys：** 集成针对进程信息的 proc 文件系统、针对设备的 devfs 文件系统以及针对伪终端的 devpts 文件系统 3 种系统文件信息。
- **/tmp：** temporary(临时) 的缩写，用来存放一些临时文件。
- **/usr：** unix shared resources(共享资源) 的缩写，这是一个非常重要的目录，用户的很多应用程序和文件都放在这个目录下，类似于 windows 下的 program files 目录。
- **/usr/bin：** 系统用户使用的应用程序。
- **/usr/src：** 内核源代码默认的放置目录。
- **/var：** variable(变量) 的缩写，这个目录中存放着在不断扩充着的东西，我们习惯将那些经常被修改的目录放在这个目录下。包括各种日志文件



## 文件与目录的基本操作

### 路径跳转、显示目录内容命令

**cd：** 更改工作目录	**pwd：** 显示路径	**ls：** 列出目录的内容

```shell
# 将工作目录切换到/root目录
cd ~

# 显示当前路径
pwd

# 列出目录内容
ls
```

![image-20211122161552013](http://image.xiaobailx.top/images/20211122161552.png)



### 显示文件内容命令

**cat：** 将文件内容全部输出到标准设备上	**-n** 显示行号

```shell
# 查看 /etc/profile 文件并显示行号
cat -n /etc/profile
```

![image-20211122112955994](http://image.xiaobailx.top/images/20211122112956.png)



**more：** 一次显示一屏内容	当文件内容过大时使用该命令	只能向后查看	Q按键退出查看  

```shell
# 查看 /etc/profile 文件的所有内容
more /etc/profile
```

![image-20211122113951270](http://image.xiaobailx.top/images/20211122113951.png)



**less：**  一次显示一屏内容，类似于more	可以向前或向后查看	Q按键退出查看	上下键进行查看

```shell
# 查看 /etc/profile 文件的所有内容
less /etc/profile
```

![image-20211122114833183](http://image.xiaobailx.top/images/20211122114833.png)



**head：** 只显示文件头几行命令	可以指定显示行数

``` shell
# 查看文件前5行内容
head -5 /etc/profile
```

![image-20211122114429563](http://image.xiaobailx.top/images/20211122114429.png)



**tail：** 只显文件示尾几行命令	可以指定显示行数

```shell
# 查看文件最后5行内容
tail -5 /etc/profile
```

![image-20211122114516524](http://image.xiaobailx.top/images/20211122114516.png)



### 文件内容查询命令

**grep：** 查找文件内容	 **-n** 显示行数	**-v** 反向查找

```shell
#查看文件中包含root字符串的行
grep root /etc/passwd
#查看进行中是否存在ssh的进程
ps -ef | grep sshd
```

![image-20211122115756609](http://image.xiaobailx.top/images/20211122115756.png)



### 文件查找命令

**find：** 在指定目录下查找文件	**-name** 指定查找文件的名称

```shell
# 在/目录下查找passwd文件
find / -name "passwd"
```

![image-20211122121915351](http://image.xiaobailx.top/images/20211122121915.png)



### 文本处理命令

**sort：**  对文件内容中各行进行排序

```shell
# 对/etc/passwd文件内容进行排序显示
sort /etc/passwd
```

![image-20211122122104952](http://image.xiaobailx.top/images/20211122122105.png)



**uniq：**  输出文件中的相邻的重复行	**-d** 显示重复行	**-u** 显示不重复行	**-c** 显示重复行数

```shell
# 显示 /root/helloworld.sh 文件内容
more helloworld.sh

# 输出 /root/helloworld.sh 文件中相邻的重复行
uniq -d helloworld.sh

# 输出 /root/helloworld.sh 文件中相邻的不重复行
uniq -u helloworld.sh

# 输出 /root/helloworld.sh 文件中相邻的重复行数
uniq -c helloworld.sh
```

![image-20211122123349518](http://image.xiaobailx.top/images/20211122123349.png)



### 文本内容统计命令

**wc：** 统计文本内容	**-l**  统计行数	**-w** 统计字数

```shell
# 查看/etc/hosts文件的行数和字符数
wc -l /etc/hosts
wc -n /etc/hosts
```

![image-20211122124104645](http://image.xiaobailx.top/images/20211122124104.png)



### 文本比较命令

**comm：** 比较文件相同和不同的内容	**-12**  显示文件 A 与文件 B 都存在的行	**-23**  只显示文件 A 与文件 B 的不同内容

```shell
# 创建a文件，a.txt文件内容：a b c
echo a >> a.txt
echo b >> a.txt
echo c >> a.txt

# 创建b文件，b文件内容: a b d
echo a >> b.txt
echo b >> b.txt
echo d >> b.txt

#比较a.txt和b.txt文件相同内容
comm -12 a.txt b.txt
#比较a.txt和b.txt文件不同内容
comm -23 a.txt b.txt
```

![image-20211122125133379](http://image.xiaobailx.top/images/20211122125133.png)



**diff：** 逐行比较两个文本文件，列出不同的内容	不需要对文件进行排序

```shell
#比较2个文件不同的内容
diff a.txt b.txt
```

![image-20211122125345454](http://image.xiaobailx.top/images/20211122125345.png)



### 文件的创建、复制、移动和删除命令

**touch：** 创建空白文件

```shell
#创建空白文件a.txt
touch a.txt
```

**cp：**  复制文件和目录	**-r**  递归复制	

```shell
# 复制文件：将/root/a.txt文件复制到/root/dir1目录下，并将文件命名为aa.txt
cp a.txt dir1/aa.txt
```

![image-20220215094714942](http://image.xiaobailx.top/images/20220215094722.png)

```shell
# 复制目录：将/root/dir1目录复制到/root/dir2目录下
cp -r /etc /home
ls /home
```

![image-20211122153425192](http://image.xiaobailx.top/images/20211122153425.png)



**mv：** 移动文件和目录 + 重命名

```shell
# 将/root/dir2目录移动到/root/dir1下面
mv dir2 dir1
```

![image-20211122153747277](http://image.xiaobailx.top/images/20211122153747.png)

```shell
# 将dir1目录重命名为dir11
mv dir1 dir11
```

![image-20211122154430403](http://image.xiaobailx.top/images/20211122154430.png)



**rm：** 删除文件与目录	**-r**  递归删除	 **-f**  强制删除

```shell
# 删除文件
rm a.txt
```

![image-20211122155001892](http://image.xiaobailx.top/images/20211122155001.png)

```shell
# 删除目录
rm -rf /home/dir3
```

![image-20211122155524454](http://image.xiaobailx.top/images/20211122155524.png)



### 文件链接命令

**ln：** 创建连接

```shell
# 创建bak/index.html文件的软链接： index
ln bak/index.html index
```

![image-20211122160936259](http://image.xiaobailx.top/images/20211122160936.png)



### 目录的创建和删除命令

**mkdir：** 创建目录	**-p**  如果目录存在就创建

```shell
#创建dirmk目录
mkdir dirmk
```

**rmdir：**  删除目录	**-p**  递归删除目录

```shell
#删除dirmk目录
rmdir dirmk
```

![image-20211122161156801](http://image.xiaobailx.top/images/20211122161156.png)



## 文件与目录权限管理

### 文件/目录访问权限

```shell
#使用ls -l 命令，查看文件或目录的相关权限
ls -l
```

![image-20211122181416233](http://image.xiaobailx.top/images/20211122181416.png)

>**r**   读权限
>
>**w** 写权限，对目录来说,可生成文件与子目录或删除文件与子目录
>
>**x**  执行权限，对目录来说,可查找该目录下内容
>
>**-**  表示没有任何权限
>
>
>
>例：rw- r-- ---
>**rw-** 表示当前文件对拥有者的权限
>
>**r--** 表示当前文件对同组人的权限
>
>**---** 表示当前文件对其他人的权限
>
>
>
>在添加或者删除某个权限的时候：
>**u** 表示拥有者
>
>**g** 表示同组人
>
>**o** 表示其他人
>
>**a** 表示所有人



### 改变文件/目录的访问权限

**chmod**

- 使用字母修改访问权限

  ```shell
  chmod u+x b.txt
  chmod g-r b.txt
  chmod u+r,g-2 b.txt
  chmod a=rw b.txt
  ```

- 使用数字修改访问权限

  - **x** 执行权限表示十进制数字 1
  - w 写权限表示十进制数字 2
  - **r** 读权限 十进制数字 4

  ```shell
  //7一定是1+2+4所得，表示拥有者、同组人、其他人都是可读可写可执行
  chmod 777 b.txt
  ```

![image-20211122182551883](http://image.xiaobailx.top/images/20211122182551.png)



### 更改文件/目录的所有权

**chown：** 详见[Linux用户管理]()



## 文件与目录的打包与压缩

### 文档压缩

**gzip：**对文件进行压缩和解压缩，其扩展名为 .gz，只能对文件操作，压缩后会默认删除原文件	

**-c** 把压缩后的文件输出到标准输出中	**-d** 对压缩文件进行解压缩	**-r** 递归压缩指定目录下及子目录下的所有文件	**-l** 列出压缩文件信息

```shell
#压缩当前目录下所有的文件
gzip *
```

![image-20211122162841451](http://image.xiaobailx.top/images/20211122162841.png)

```shell
# 压缩指定的文件b.txt。压缩后保留原文件
gzip -c b.txt > b.txt.gz
```

![image-20211122163329899](http://image.xiaobailx.top/images/20211122163329.png)

```shell
#将b.txt.gz文件解压到当前目录下
gzip -d b.txt.gz
```

![image-20211122163753784](http://image.xiaobailx.top/images/20211122163753.png)

```shell
#压缩dir1目录下的所有文件
gzip -r dir1/
```

![image-20211122164100726](http://image.xiaobailx.top/images/20211122164100.png)

```shell
#列出压缩文件信息
gzip -l b.txt.gz
# 列表含义
压缩文件的大小 未压缩文件的大小 压缩比 未压缩文件的名称
```

![image-20211122164355089](http://image.xiaobailx.top/images/20211122164355.png)



### 文件归档

**tar：** 将多个文件一起保存到一个单独的磁带或磁盘中进行归档，使用 tar 命令归档的包通常称为 tar 包，其文件都是以 .tar 结尾
**-c**  将多个文件或目录进行打包	**-f**  指定包的文件名	**-v** 显示打包文件过程

```shell
#将dir1、dir2打包到dir.tar包中
tar -cf dir.tar dir1 dir2
```

![image-20211122170405368](http://image.xiaobailx.top/images/20211122170405.png)

**-x**   对 tar 包做解压操作	**-C**  解压到指定目录下

```shell
#将dir.tar包解压至dir目录下
tar -xf dir.tar -C dir
```

![image-20211122170807835](http://image.xiaobailx.top/images/20211122170807.png)

**-t**  查看压缩包文件

```shell
#查看压缩包内容
tar -tvf dir.tar
```

![image-20211122172040447](http://image.xiaobailx.top/images/20211122172040.png)

**-z**  支持gzip解压文件

```shell
#压缩打包dir目录为tar.gz压缩文件
tar -zcvf dir.tar.gz dir
```

![image-20211122173103569](http://image.xiaobailx.top/images/20211122173103.png)



### zip压缩

**zip：** 压缩文件或目录，压缩文件为 .zip 格式文件是 Windows 和 Linux 系统都通用的压缩文件类型，需要指定压缩之后的压缩包名。

> centos系统默认没有提供zip和unzip命令。我们可以使用 yum 执行安装zip命令
> yum install unzip zip

```shell
# 使用yum安装zip
yum install unzip zip

# 压缩a.txt文件，文件名为a.zip
```

![image-20211122175917359](http://image.xiaobailx.top/images/20211122175917.png)

