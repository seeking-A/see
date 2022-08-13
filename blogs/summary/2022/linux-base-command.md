---
title: Linux常用命令
date: 2022-07-07
tags:
 - Linux
categories:
 - Linux
---

## 常见系统命令

export 查看或修改环境变量

```shell
# 例：临时修改命令提示符为字符串$
export PS1=$
# 例：临时修改命令提示符显示系统时间 时间使用\t 表示
export PS1="[\u@\h \t \W]\$"
```

man 查看linux系统的手册

```shell
# 例：查看ls命令如何使用
man ls 
# 回车按钮： 帮助文档下一行
# Q按钮： 退出帮助文档
```

help 查看帮助文档

```shell
#案例： 查看cd命令如何使用？
help cd
#案例： 查看mkdir命令如何使用?
mkdir --help
```

info 支持文件的链接跳转，比man命令更具有交互性

```shell
#案例： 查看ls命令如何使用？
info ls
# 点击N 表示下一节点的文档内容
```

systemtctl 系统管理

```shell
#启动：
systemctl start name.service

#停止：
systemctl stop name.service

#重启：
systemctl restart name.service

#查看状态：
systemctl status name.service

#查看某服务当前激活与否的状态：
systemctl is-active name.service

#查看所有已经激活的服务：
systemctl list-units --type|-t service

#设定某服务开机自启：
systemctl enable name.service

#设定某服务开机禁止启动：
systemctl disable name.service

#查看服务是否开机自启：
systemctl is-enabled name.service

#开机并立即启动或停止：
systemctl enable --now postfix
systemctl disable --now postfix

#重新加载
systemctl reload name.service

#重新加载服务配置文件
systemctl daemon-reload
```

其他常用系统命令

```shell
# 清屏
clear 
# 查看历史输入命令
history 
# 关机
sudo reboot 
```

```
title: 枚举类和注解
date: 2018-12-15
tags:
 - JavaSe
categories:
 - Java基础
```

## 

## 目录的基本操作

### 目录内容显示命令

**cd：** 更改工作目录	**pwd：** 显示路径	**ls：** 列出目录的内容

```shell
# 将工作目录切换到/root目录
cd ~

# 显示当前路径
pwd

# 列出目录内容
ls
```

### 目录的管理命令

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



## 文件的基本操作

### 文件内容显示命令

**cat：** 将文件内容全部输出到标准设备上	**-n** 显示行号

```shell
# 查看 /etc/profile 文件并显示行号
cat -n /etc/profile
```



**more：** 一次显示一屏内容	当文件内容过大时使用该命令	只能向后查看	Q按键退出查看  

```shell
# 查看 /etc/profile 文件的所有内容
more /etc/profile
```



**less：**  一次显示一屏内容，类似于more	可以向前或向后查看	Q按键退出查看	上下键进行查看

```shell
# 查看 /etc/profile 文件的所有内容
less /etc/profile
```



**head：** 只显示文件头几行命令	可以指定显示行数

``` shell
# 查看文件前5行内容
head -5 /etc/profile
```



**tail：** 只显文件示尾几行命令	可以指定显示行数

```shell
# 查看文件最后5行内容
tail -5 /etc/profile
```



### 文件内容查询命令

**grep：** 查找文件内容	 **-n** 显示行数	**-v** 反向查找

```shell
#查看文件中包含root字符串的行
grep root /etc/passwd
#查看进行中是否存在ssh的进程
ps -ef | grep sshd
```



### 文件查找命令

**find：** 在指定目录下查找文件	**-name** 指定查找文件的名称

```shell
# 在/目录下查找passwd文件
find / -name "passwd"
```



### 文件的管理命令

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

```shell
# 复制目录：将/root/dir1目录复制到/root/dir2目录下
cp -r /etc /home
ls /home
```



**mv：** 移动文件和目录 + 重命名

```shell
# 将/root/dir2目录移动到/root/dir1下面
mv dir2 dir1
```

```shell
# 将dir1目录重命名为dir11
mv dir1 dir11
```



**rm：** 删除文件与目录	**-r**  递归删除	 **-f**  强制删除

```shell
# 删除文件
rm a.txt
```

```shell
# 删除目录
rm -rf /home/dir3
```



## 文件和目录的权限管理

### 访问权限

```shell
#使用ls -l 命令，查看文件或目录的相关权限
ls -l
```

**r**   读权限	**w** 写权限，对目录来说,可生成文件与子目录或删除文件与子目录	**x**  执行权限，对目录来说,可查找该目录下内容	

**-**  表示没有任何权限

例：rw- r-- ---
**rw-** 表示当前文件对拥有者的权限	**r--** 表示当前文件对同组人的权限	**---** 表示当前文件对其他人的权限



在添加或者删除某个权限的时候：
**u** 表示拥有者	**g** 表示同组人	**o** 表示其他人	**a** 表示所有人



### 修改访问权限

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



### 文件和目录的打包与压缩

### 文档压缩

**gzip：**对文件进行压缩和解压缩，其扩展名为 .gz，只能对文件操作，压缩后会默认删除原文件	

**-c** 把压缩后的文件输出到标准输出中	**-d** 对压缩文件进行解压缩	**-r** 递归压缩指定目录下及子目录下的所有文件	**-l** 列出压缩文件信息

```shell
#压缩当前目录下所有的文件
gzip *
```

```shell
# 压缩指定的文件b.txt。压缩后保留原文件
gzip -c b.txt > b.txt.gz
```

```shell
#将b.txt.gz文件解压到当前目录下
gzip -d b.txt.gz
```

```shell
#压缩dir1目录下的所有文件
gzip -r dir1/
```

```shell
#列出压缩文件信息
gzip -l b.txt.gz
# 列表含义
压缩文件的大小 未压缩文件的大小 压缩比 未压缩文件的名称
```



### 文件归档

**tar：** 将多个文件一起保存到一个单独的磁带或磁盘中进行归档，使用 tar 命令归档的包通常称为 tar 包，其文件都是以 .tar 结尾
**-c**  将多个文件或目录进行打包	**-f**  指定包的文件名	**-v** 显示打包文件过程

```shell
#将dir1、dir2打包到dir.tar包中
tar -cf dir.tar dir1 dir2
```

**-x**   对 tar 包做解压操作	**-C**  解压到指定目录下

```shell
#将dir.tar包解压至dir目录下
tar -xf dir.tar -C dir
```

**-t**  查看压缩包文件

```shell
#查看压缩包内容
tar -tvf dir.tar
```

**-z**  支持gzip解压文件

```shell
#压缩打包dir目录为tar.gz压缩文件
tar -zcvf dir.tar.gz dir
```



### zip压缩

**zip：** 压缩文件或目录，压缩文件为 .zip 格式文件是 Windows 和 Linux 系统都通用的压缩文件类型，需要指定压缩之后的压缩包名。

> centos系统默认没有提供zip和unzip命令。我们可以使用 yum 执行安装zip命令
> yum install unzip zip

```shell
# 使用yum安装zip
yum install unzip zip

# 压缩a.txt文件，文件名为a.zip
zip a.zio a.txt
```



## 用户与用户组管理

### 用户管理

**useradd：** 添加用户	**-d**  指定用户登入时的目录	**-g**  初始群组	**-m** 自动创建用户的目录

```shell
#创建xiaobai用户，指定用户所属root组，家目录为/home/xiaobai
useradd xiaobai -d /home/xiaobai -g root -m
```



**passwd：** 修改指定用户密码，缺少指定用户名，默认为当前用户修改密码

```shell
#修改新建用户xiaobai用户的密码,密码为xiaobai
passwd xiaobai
```



**su：** 切换用户

```shell
#root用户切换到jack用户
su jack
```



 **usermod：**修改用户属性	**-g** 改变用户的组

```shell
#查看xiaobai用户所属的组
groups
#修改当前用户所属的组为bin组
usermod -g bin xiaobai
```



**userdel：**删除用户	**-f** 强制删除用户	**-r** 同时删除用户及用户家目录

```shell
userdel -rf xiaobai
```



### 用户组管理

**groupadd：** 创建用户组

```shell
#创建用户组user_team
groupadd user_team
```



**groupmod：**修改用户组属性	**-n** 新组名	**-g** 新的组标识号

```shell
#将用户组user_team重命名为usermod_team
groupmod -n usermod_team user_team
```



**groupdel：**删除用户组

```shell
#删除用户组名usermod_team
groupdel usermod_team
```



## 进程管理

**ps：** 查看进程	-**ef**  显示系统中所有进程的全面信息	**aux**  显示所有用户有关进程的所有信息

```shell
#查看系统全部的进程
ps -ef
#显示所有用户有关进程的所有信息
ps -aux
```



**top：**动态显示进程的过程

```shell
#动态查看当前进程信息
# -c 列出完整指令信息
top -c
```



**kill：**终止进程		**-9** 强制终止进程

```shell
#强制停止掉进程id为123的进程
kill -9 123
```

