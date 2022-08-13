---
title: Linux基本操作
date: 2021-11-22
tags:
 - Linux
categories:
 - Linux
---

### 常见系统命令

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

其他常用系统命令

```shell
# 清屏
clear 
# 查看历史输入命令
history 
# 关机
sudo reboot 
```



### 快捷键

Tab键：自动补全 + 内容提示

上下键：查看历史

Ctrl+A：移动光标到开头

Ctrl + D：登出

Ctrl + C：终止当前命令

Ctrl + L：清屏

Ctrl + R：搜索历史命令

Ctrl + Z：暂停

