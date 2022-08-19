---
title: LNMP环境搭建
date: 2021-11-26
tags:
 - Linux
categories:
 - Linux
---

## Nginx

### 安装

1. 下载安装包：[nginx: Linux packages](http://nginx.org/en/linux_packages.html)
2. 安装 yum-installs

```shell
yum install yum-utils
```

3. 创建/etc/yum.repos.d/nginx.repo，文本内容如下

```shell
touch /etc/yum.repos.d/nginx.repo
```

```shell
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
```

4. 下载安装

```shell
#切换库中资源
yum-config-manager --enable nginx-mainline
# 安装Nginx
yum install nginx -y
```



### 使用

#### 静态服务器

1. 编辑配置文件

```shell
vi /etc/nginx/nginx.conf
```

2. 修改以下内容

```shell
user root; #修改为root用户
worker_processes  1; #工作进程数：与CPU核心数有关
error_log /var/log/nginx/error.log warn;
pid    /var/run/nginx.pid;
events {
     worker_connections  1024;
}
# HTTP模块配置
http {
      #每一个server{}都是一个虚拟主机
     server {
         listen			80;        #添加端口号
         server_name	192.168.171.136; #添加服务器ip地址
		#指定编码格式
         charset		utf-8,GBK;
		#添加静态文件地址
		location / {
              #定义网站根目录
			root  /var/www/html/;
			#自动创建索引，显示目录内容
			autoindex on;
		}
	}
}
```

> 如果使用80端口，注意先关闭httpd服务器

3. 在 /var/www/html/ 目录下提供静态文件
4. 启动nginx服务器

```shell
#查看nginx命令
whereis nginx
#开启nginx服务器
nginx
# 重启
nginx -s reload
```

5. 访问浏览器



#### 虚拟主机

1. 编辑配置文件

```shell
vi /etc/nginx/nginx.conf
```

2. 修改以下内容

```shell
user root; #修改为root用户
worker_processes  1; #工作进程数：与CPU核心数有关
error_log /var/log/nginx/error.log warn;
pid    /var/run/nginx.pid;
events {
     worker_connections  1024;
}
# HTTP模块配置
http {
      #每一个server{}都是一个虚拟主机
     server {
         listen			80;        #添加端口号
         #可基于域名、IP地址、端口号进行配置
         server_name	www.xiaobai.com; #添加服务器域名
		#指定编码格式
         charset		utf-8,GBK;
		#添加静态文件地址
		location / {
              #定义网站根目录
			root  /var/www/html/;
			#自动创建索引，显示目录内容
			autoindex on;
		}
	}
}
```

3. 配置hosts文件，添加域名与 ip 映射关系

```shell
127.0.0.1  www.xiaobai.com
```

4. 启动 nginx 服务器

```shell
# 重启
nginx -s reload
```

5. 访问浏览器



#### 负载均衡

1. 编辑配置文件

```shell
vi /etc/nginx/nginx.conf
```

2. 修改以下内容

```shell
user root; #修改为root用户
worker_processes  1; #工作进程数：与CPU核心数有关
error_log /var/log/nginx/error.log warn;
pid    /var/run/nginx.pid;
events {
     worker_connections  1024;
}
# HTTP模块配置
http {
	#负载均衡模块信息： 默认使用轮询的方式进行
	#当其中的一台服务器停止后，可以切换使用另一台服务器
	upstream cloud.briuptest.com{
		#目标服务器1
		server 192.168.171.141:80;
		#目标服务器2
		server 47.103.208.194:80;
	}
      #每一个server{}都是一个虚拟主机
     server {
         listen			80;        #添加端口号
         #可基于域名、IP地址、端口号进行配置
         server_name	www.xiaobai.com; #添加服务器域名
		#指定编码格式
         charset		utf-8,GBK;
		#添加静态文件地址
		location / {
              #定义网站根目录
			root  /var/www/html/;
			#proxy_pass指令用于设置被代理服务器的地址。可以是主机名称、IP地址加端口号的形式
			proxy_pass  http://www.xiaobai.com; 
			#自动创建索引，显示目录内容
			autoindex on;
		}
	}
}
```

3. 配置hosts文件，添加域名与 ip 映射关系

```shell
127.0.0.1  www.xiaobai.com
```

4. 启动 nginx 服务器

```shell
#重启
nginx -s reload
```

5. 访问浏览器



## MySQL

### 安装

1. 访问官网选择 yum 下载：[MySQL :: Download MySQL Yum Repository](https://dev.mysql.com/downloads/repo/yum/)

2. 使用 wget 命令下载复制的 url

```shell
https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
```

3. 安装rpm文件，安装成功后，yum数据源发生改变

```shell
yum install -y mysql80-community-release-el7-3.noarch.rpm
```

4. 编辑mysql数据源文件，修改下载的版本为5.7

```shell
vi /etc/yum.repos.d/mysql-community.repo
```

```shell
enable=0 #修改默认值
```

5. 安装mysql

```shell
yum install -y mysql-community-server
```



### 使用

1. 启动 mysql 服务

```shell
systemctl start mysqld.service
```

2. 编辑 mysql 配置文件，跳转 mysql 权限验证

```shell
vi /etc/my.cnf
```

添加以下内容

```shell
#跳过权限验证
skip-grant-table=1
```

3. 重启mysql服务器，使用root用户登录数据库

```shell
systemctl restart mysqld.service
mysql -u root -p
```

4. 修改 root 用户密码

```shell
mysql> show databases;
mysql> use mysql;
mysql> update user set password = password('newPasswd') where user = 'root';
```

5. 重启mysql服务

```shell
   systemctl restart mysqld.service
```



### 远程连接

1. root用户登录 mysql 数据库

```shell
mysql -u root -proot
use mysql
```

2. 执行修改 sql 语句

```shell
select user,host,password from user;
update user set host='%' where user='root';
flush privileges;
```




## Java

1. 下载安装包：https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html

2. 解压文件

```shell
#解压文件到/opt目录下
tar -zxvf jdk-8u281-linux-x64.tar.gz -C /opt/
```

3. 配置全局环境变量

```shell
export JAVA_HOME=/opt/jdk1.8.0_281
export CLASSPATH=.
export PATH=$JAVA_JOME/bin:$PATH
```

4. 环境变量生效

```shell
source /etc/profile
```

5. 测试

```shell
java -version
```



## Tomcat

### 下载

1. 访问官网 https://tomcat.apache.org/download-80.cgi
2. 选择下载 tar.gz 文件，右键复制下载连接
3. 使用wget命令下载

```shell
https://mirrors.bfsu.edu.cn/apache/tomcat/tomcat-8/v8.5.65/bin/apache-tomcat-8.5.65.tar.gz
```

4. 将压缩包解压到 /opt 目录下

```shell
tar -zxvf apache-tomcat-8.5.65.tar.gz -C /opt/
```



### 部署

1. 启动

```shell
cd /opt/apache-tomcat-8.5.65/bin/
#执行启动脚本
./startup.sh
#以控制台方式启动
./catalina.sh run
#后台方式启动tomcat
nohup ./catalina.sh run
```

2. 端口占用

```shell
#查询8080端口占用的进程
netstat -lnp | grep 8080
#杀死进程
kill -9 进程号
```

