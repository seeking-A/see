---
title: Ubuntu和CentOS命令比较
date: 2022-07-02
tags:
 - Linux
categories:
 - Linux
---

之前学习 Linux 系统时学习的是 CentOS 系统，到公司后其开发环境是基于 Ubuntu 系统的。在使用过程中，发现因为 Ubuntu 的命令和CentOS 的命令存在一些区别，在此总结记录一下。

**服务管理(以aoache为例)**

| 系统               | CentOS              | Ubuntu                      |
| ------------------ | ------------------- | --------------------------- |
| 启动服务           | service httpd start | /etc/init.d/apache start    |
| 停止服务           | service httpd stop  | /etc/init.d/apache stop     |
| 设置服务开机自启动 | chkconfig httpd on  | update-rc.d apache defaults |
| 设置禁止服务自启动 | chkconfig httpd off | update-rc.d apache purge    |



**在线软件安装**

| 系统           | CentOS              | Ubuntu                  |
| -------------- | ------------------- | ----------------------- |
| 软件包         | *.rpm               | *.deb                   |
| 源配置文件     | /etc/yum.conf       | /etc/apt/source.list    |
| 更新软件包列表 | 每次运行时自动更新  | apt-get update          |
| 在线安装软件   | yum install package | apt-get install package |
| 安装本地软件   | yum install pkg.rpm | dpkg -i pkg.deb         |
| 删除软件包     | yum -e package      | apt-get remove package  |
| 软件包升级     | yum update          | apt-get update          |
| 软件包升级测试 | yum check-update    | apt-get -s update       |
| 升级系统       | yum upgrade         | apt-get dist-upgrade    |

参考文章：[centos和ubuntu命令区别总结列表](