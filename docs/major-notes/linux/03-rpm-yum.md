---
title: RPM管理器
date: 2021-11-25
tags:
 - Linux
categories:
 - Linux
---

### RPM 简介

Redhat Package Manager 简称 RPM，是红帽公司为RHEL开发的专用包管理器。可以实现软件的查询、安装、卸载、升级和验证等功能。

### RPM 安装

使用 yumdownloader 命令安装 RPM 命令。

```shell
# 下载安装yu-utils软件
yum install yum-utils -y
# 使用yu-utils软件提供的yumdownloader 安装rpm
yumdownloader rpm
```

### 查询 RPM 软件包

**-q** 查询		**-a** 显示所有已安装的软件列表		**-f**  显示所属软件包		**-l**  显示软件包详情		**-i**  显示概要信息

- **查询所有已经安装的软件包**

```shell
# 查询所有已经安装的软件包
rpm -qa
```

![image-20211122193211379](http://image.xiaobailx.top/images/20211122193211.png)

- **查询文件所属软件包**

```shell
#查看/usr/sbin目录下文件所属软件包
rpm -qf /usr/sbin/httpd
#查看/usr/bin目录下文件所属软件包
rpm -qf /user/bin/vim
```

![image-20211122192936317](http://image.xiaobailx.top/images/20211122192936.png)

- **查询软件包所包含的文件列表**

```shell
#查看firewalld软件包信息
rpm -ql firewalld-filesystem-0.6.3-11.el7.noarch
```

![image-20211122194047275](http://image.xiaobailx.top/images/20211122194047.png)

- **查询软件包概要信息**

```shell
#查看firewalld软件包信息
rpm -qi firewalld-filesystem-0.6.3-11.el7.noarch
```

![image-20211122194352559](http://image.xiaobailx.top/images/20211122194352.png)

- **查询指定软件包是否安装**

```shell
#查看httpd服务器软件包是否安装
rpm -q httpd
#查看tree软件是否安装
rpm -q tree
```

![image-20211122200906827](http://image.xiaobailx.top/images/20211122200906.png)



### RPM 软件包的安装

详细步骤如下

1. **网络下载：** 通过网络将文件下载到本地，然后进行安装。推荐下载地址：[https://pkgs.org/](https://pkgs.org/)

2. **光盘挂载：** 安装使用的软件包存在在我们iso光盘的Package目录下，我们需要首先挂载光盘，获取到RPM软件
   包。

   - 虚拟机设置挂载光盘，相当于我们插入光盘的操作

   ![image-20211122205327961](http://image.xiaobailx.top/images/20211122205328.png)

   - 查找光盘的文件位置

   ```shell
   # 在/dev中查找光盘的文件位置
   ls -l /dev | grep cdrom
   ```

   ![image-20211122205546458](http://image.xiaobailx.top/images/20211122205546.png)

   - 挂载光盘到/media/cdrom目录下

   ```shell
   #创建挂载的目录
   mkdir /media/cdrom
   #挂载光盘到/media/cdrom目录下
   mount /dev/cdrom /media/cdrom
   ```

   

