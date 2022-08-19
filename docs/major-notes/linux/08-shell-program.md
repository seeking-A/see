---
title: shell 编程
date: 2021-11-25
tags:
 - Linux
categories:
 - Linux
---

 

## shell 编程基础

### shell 脚本基础

shell 脚本是包含若干行 shell 或者 Linux 命令的文件，以 .sh 为后缀，其文件第一行用于指定脚本解释器，例：

1. 编写shell脚本文件hello.sh

   ```shell
   vim hello.sh
   ```

   hello.sh 内容如下：

   ```shell
   #!/bin/sh
   echo 'Hello world!'
   ```

2. 添加文件可执行权限

   ```shell
   chmod 755 hello.sh
   ```

3. 执行文件

   ```shell
   ./hello.sh
   ```

   输出：Hello world!



### 输入输出重定向

使用重定向程序可将标准输入输出到文件当中。

- **<**  使用小于号实现重定向
- **>**  直接输出，覆盖原始内容

```shell
#查看目录信息输出到a文件，如果命令执行错误输出b文件
#2表示标准错误输出文件
#注意2和重定向之间没有空格
ls -l /root > a.txt 2> b.txt
```

- **>>**  附加输出，添加到文件末尾



### 管道

在一个命令的标准输出和另一个命令的标准输入之间建立一个管道。

```shell
# 查看web服务器httpd进程是否启动
ps -ef | grep httpd
# 分页显示进行信息
ps -ef | more
```



### shell 特殊字符

- **""** ： 双引号，括起的字符作为普通字符显示，特殊符号 $ 、` 、\ 除外；
- **''** ： 单引号，括起的字符作为普通字符现显示
- **``** ：括起的字符被解析为窗口命令

例：

```shell
#!/bin/sh
echo 'Hello world!'
echo "Hello world!"
echo '`pwd` is my home.'
echo "`pwd` is my home."
```

输出内容：

```shell
Hello world!
Hello world!
`pwd` is my home.
/home/xiaobai is my home.
```



### shell 脚本注释

- 单行注释： #

- 多行注释： EOF 自定义终止符

  1. EOF 格式

  ```shell
  <<EOF        //开始
  ....		
  EOF         //结束
  ```

  > EOF不是固定的，可以任意自定义。但需注意，结束符和起始符要保持一致。

  2. EOF 使用

  ```shell
  #!/bin/sh
  echo 'Hello world!'
  :<< EOF
  echo "Hello world!"
  echo '`pwd` is my home.'
  echo "`pwd` is my home."
  EOF
  ```



## shell 变量

### 系统变量

| 变量名 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| $#     | 命令行参数个数                                               |
| $n     | n 表示第N个参数 $1 $2 $3 $4                                  |
| $0     | 当前程序的名称                                               |
| $?     | 前一个命令或函数的返回码<br/>0：成功；1：不允许操作；2：文件目录不存在；5：输入输出拒绝访问；13：拒绝访问 |
| $*     | 以"参数1 参数2 参数3..."形式保存所有参数,为单个字符串        |
| $@     | 以"参数1 " "参数2 " "参数3" ...形式保存所有参数,推荐使用该方式保存参数 |
| $$     | 本程序的PID                                                  |
| $!     | 上一个命令PID                                                |



### 环境变量

| 变量名       | 作用                             |
| ------------ | -------------------------------- |
| HOME         | 用户的主目录                     |
| SHELL        | 用户在使用的shell解释器名称      |
| HISTSIZE     | 输出的历史命令记录条数           |
| HISTFILESIZE | 保存的历史命令记录条数           |
| MAIL         | 邮件保存路径                     |
| LANG         | 系统语言、语系名称               |
| RANDOM       | 生成一个随机数值                 |
| PS1          | bash解释器提示符                 |
| PATH         | 定义解释器搜索用户执行命令的路径 |
| EDITOR       | 用户默认的文本编辑器             |



### 用户变量

用户变量其变量名由 **字母、数字及下划线** 组成。变量名 **区分大小写**，且 **首字符不能为数字**。

- **赋值和声明** ： shel l下的变量无需声明即可使用，赋值同时即声明了变量。格式如下：

```shell
变量名=变量值	#等号两边没有空格
```

- **使用** ： 引用变量时，需在变量名前加上 **$** 符号。变量名包含在字符串中，需使用 **{}** 括起来。

```shell
#!/bin/sh
num=666
echo "num is ${num}"
```

> PS：为了避免变量名混淆，建议总是使用 **{}** 将变量名括起来。

- **uset 命令** ： 清空某一变量

  ```shell
  num=666
  echo ${num}
  unset num	#清空num变量
  echo ${num}
  ```

- **readonly 命令** ： 设置变量只读

  ```shell
  readonly name
  name=dabai	#修改name的值
  ```

  

### 数字和数组

- 默认赋值是对字符串赋值

- **declare 命令** ： 对数组或数字进行声明

  **i** ： 声明数字		**a** ： 声明数组 

```shell
#声明数字
declare -i a=1
declare -i b=2
declare -i c=$a+$b
#声明数组
#参数a可以省略
declare -a arr=(a b c)
```



## shell 运算符

shell 运算符与 c 语言的运算符基本类似





## shell 流程控制

### 条件判断

#### if...else...

```shell
if condition 
then
     statement1
else
     statement2
fi
```



#### if...elif...else

```shell
if condition1 then
     statement1
elif condition2 then
     statement2
else
     statementn
fi
```

例如：

```shell
#!/bin/sh
read age
if (($age<14));	then
	echo "儿童"
elif (($age<18)); then
	echo "青少年"
else
	echo "成年人"
fi
```

> 只要括号中的运算符、表达式符合c语言的运算规则，都可用在$((exp))。注意，表达式后边必须以 **；** 分割



### 分支结构

#### test

test 命令用来判断 **数值、字符串和文件** 三个方面的某个条件是否成立，通常和 if 语句一起使用。

1. 数字判断

| 参数 | 用途                                                         |
| ---- | ------------------------------------------------------------ |
| le   | 小于等于 *[less](javascript:;) [than](javascript:;) [or](javascript:;) [equal](javascript:;) [to](javascript:;)* |
| ge   | 大于等于 [great](javascript:;) [than](javascript:;) [or](javascript:;) |
| eq   | 等于 [equal](javascript:;)                                   |
| ne   | 不等于 not equals                                            |
| lt   | 小于 *[less](javascript:;) [than](javascript:;)*             |
| gt   | 大于 *[greater](javascript:;) [than](javascript:;)*          |

```shell
#!/bin/sh
if test $# -eq 0
then
	echo "没有输出任何参数"
fi
```

test 命令可以简写为 **[]**

```shell
#!/bin/sh
if test [ $# -eq 0 ]
then
	echo "没有输出任何参数"
fi
```

> 注意：[] 和 expression 之间的必须空格，否则会导致语法错误

2. 字符串判断

| 选 项                    | 作 用                                                 |
| ------------------------ | ----------------------------------------------------- |
| -z str                   | 判断字符串 str 是否为空。                             |
| -n str                   | 判断宇符串 str 是否为非空。                           |
| str1 = str2 str1 == str2 | `=`和`==`是等价的，都用来判断 str1 是否和 str2 相等。 |
| str1 != str2             | 判断 str1 是否和 str2 不相等。                        |

3. 文件判断

- 判断文件类型及是否存在
|  选 项  | 作 用                                  |
| :-----: | :------------------------------------- |
| -d file | 判断文件是否存在，并且是否为目录文件。 |
| -e file | 判断文件是否存在。                     |
| -f file | 判断文件是否存在，井且是否为普通文件。 |
| -s file | 判断文件是否存在，并且是否为非空。     |
- 判断文件是否存在及权限
|  选 项  | 作 用                                    |
| :-----: | ---------------------------------------- |
| -r file | 判断文件是否存在，并且是否拥有读权限。   |
| -w file | 判断文件是否存在，并且是否拥有写权限。   |
| -x file | 判断文件是否存在，并且是否拥有执行权限。 |
- 文件之间比较
| 选 项           | 作 用                                    |
| --------------- | ---------------------------------------- |
| file1 -nt file2 | 判断 file1 的修改时间是否比 file2 的新。 |
| file -ot file2  | 判断 file1 的修改时间是否比 file2 的旧。 |



#### case

当分支较多，并且判断条件比较简单时，使用 case in 语句。

```shell
case expression in
     pattern1) statement1
     ;;
     pattern2) statement2
     ;;
     pattern3) statement3
     ;;
     ……
     *) statementn
esac
```

例：

```shell
#!/bin/bash
case "$#" in
	0) echo "没有输入参数"
	1) echo "输入一个参数"
	2) echo "输入两个参数"
    *) echo "输入多个参数"
esac  
```





### 循环结构

#### for

- 格式一

  ```shell
  for ((exp1;exp2;exp3))
  do
  	statements
  done
  ```

- 格式二

  ```shell
  for variable in value_list
  do
       statements
  done
  ```
  



#### while

```shell
# 当表达式为true执行
while condition
do
     statements
done
```

例如：

```shell
while [ $num -lt 10 ]
do
	echo $num
	let num++
done  
```

> let 是 bash 中用于计算的工具，用于执行一个或多个表达式，变量计算中不需要加上 $ 来表示变量



#### until

```shell
# 当表达式为false执行
until condition
do
 statements
done
```



#### 关键字

- break： 在正常结束之前推出当前循环
- continue： 不执行本次循环
- exit： 终止脚本程序并返回值，通过 **$?** 可以获取该返回值