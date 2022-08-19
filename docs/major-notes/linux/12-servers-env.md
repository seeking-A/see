---
title: 常见Linux服务器部署
date: 2021-11-26
tags:
 - Linux
categories:
 - Linux
---

## Apache服务器

### 部署流程

1. 安装

```shell
yum install httpd -y
```

2. 启动

```shell
#启动Apache服务器
systemctl start httpd.service
#查看启动状态
systemctl status httpd.service
#停止Apache服务器
systemctl stop httpd.service
#重启Apache服务器
systemctl restart httpd.service
```

3. 部署

```shell
#查看Apache服务器配置文件httpd.conf
more /etc/httpd/conf/httpd.conf
#查找服务器的根目录信息ServerRoot
cat /etc/httpd/conf/httpd.conf | grep DocumentRoot
#在DocumentRoot对应的目录下创建index.html页面
vi /var/www/html/index.html
```



## FTP服务器

### 部署流程
1. 安装

```shell
yum install -y vsftpd
```

2. 配置

   关闭 selinux (linux的一个安全子系统)

```shell
#查看selinux是否开启,默认情况下是强制开启
getenforce
#关闭selinux
vi /etc/selinux/config
#修改配置文件内容
SELINUX=disabled
#启动系统，查看是否开启,disabled表示关闭
reboot
```

3. 启动FTP服务器

```shell
#查看ftp服务器状态
systemctl start vsftpd.service
#查看ftp服务器状态
systemctl status vsftpd.service
#停止ftp服务器
systemctl stop vsftpd.service
```

4. 连接

   - 匿名用户：不需要账号和密码

   ```shell
   #修改配置文件
   vim /etc/vsftpd.conf
   ```
   ​	配置文件如下：

   ```shell
   #要求下列权限开启
   #默认：允许匿名用户登录
   anonymous_enable=YES
   #修改：允许匿名用户有创建目录权限
   anon_mkdir_write_enable=YES
   #修改：允许匿名用户可以上传文件
   anon_upload_enable=YES
   #添加：匿名用户登入时不需要密码
   no_anon_password=YES
   #添加：允许匿名用户下载可阅读文档
   anon_world_readable_only=YES
   #添加：允许匿名用户有其他权限
   anon_other_write_enable=YES
   ```

   ```shell
   #保存修改内容后退出重启ftp服务器
   systemctl restart vsftpd.service
   #在/var/ftp/pub目录下创建文件或目录
   touch /var/ftp/pub/a.txt
   #cmd窗口中进行连接测试
   ftp 192.168.171.132
   ```

   - 本地用户：拥有账号的用户，也称真实用户
   
     常用配置本地用户权限参数如下
   
   ```shell
   #默认：当前系统中的用户可以登录
   local_enable=YES
   #默认：定义所有本地用户的登录后根目录,默认为用户的家目录
   local_root=
   #禁止userlist_file文件中的用户登录ftp服务器
   userlist_enable=YES
   #允许userlist_file文件中的用户登录ftp服务器
   userlist_deny=NO
   #锁定文件chroot_list中用户登录后，只能在自己的家目录，默认不锁定
   chroot_list_enable=YES
   ```
   
    ```shell
   #编辑userlist_file文件内容,添加或删除用户
   vi /etc/vsftpd/user_list
   #修改完成后保存退出并重启ftp服务器
   systemctl restart vsftpd.service
    ```
   
   
   
### FTP常用命令

| 命令              | 说明                                |
| ----------------- | ----------------------------------- |
| ftp <ipaddr>      | 登入指定ftp服务器                   |
| bye               | 终止主机FTP进程，并退出FTP管理方式  |
| get <remote-file> | 从远端足迹中传送至本机中            |
| open host         | 新建连接                            |
| put <local-file>  | 将本地一个文件传送至远端主机        |
| ?                 | 显示全部命令                        |
| !                 | 从ftp子系统退出到本地系统中执行命令 |

   

