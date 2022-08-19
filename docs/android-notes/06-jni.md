# JNI学习笔记

## JNI简介

### JNI概述

JNI 全称 Java Native Interface（Java本地开发接口），是一种协议。通过 JNI 就可以让就 Java 调用 C 语言或者 C++ 代码，并且可以让C 语言调 Java 代码。

![](http://image.xiaobailx.top/images/202208131603957.png)



### JNI的作用

1. 通过JNI 技术，可以扩展 Android手机 的功能，如 wifi 热点；
2. native coder执行高效：大量的运算（极品飞车），万能解码（ffmpeg）,Opengl(3D 渲染)；
3. 实现代码复用：ffmpeg,onencv(人脸识别库)，7–zip；
4. 使用场景：考虑特殊情况



### JNI的使用

1. 熟悉 C 语言
2. 熟悉 JNI 开发流程



## C语言基础

### HelloWorld

```c
#include <stdio.h>//#include引用标准依赖库
//stdio: standard input&output 标准的输入输出; stdlib:标准的C语言函数库; .h 头文件的后缀；包含一些函数 
#include <stdlib.h>

main()//相当于Java的main函数
{
      printf("Hello world !\n");//输出函数; \n是回车换行 
      system("calc");//调起计算器 
      system("mspaint");//调起画版 
      system("services.msc");//调起画版 
      system("java Hello");//执行字节码 
      system("pause");//让docs命令行执行pause命令，作用是控制台停留。 
} 

```



### 基本数据类型

| 数据类型 | Java语言 | C语言 |
| -------- | -------- | ----- |
| byte     | 1byte    | –     |
| short    | 2byte    | 2byte |
| int      | 4byte    | 4byte |
| **long** | 8byte    | 4byte |
| float    | 4byte    | 4byte |
| double   | 8byte    | 8byte |
| **char** | 2byte    | 1byte |
| boolean  | 1byte    | 1byte |
| signed   | –        | 4byte |
| unsigned | –        | 4byte |
| void     | –        | 1byte |

总结：

1. C 语言中 char 类型是1个字节（不可以表示汉字），Java语言是2个字节（可以表示汉字）；
2. C 语言中 long 类型是4个字节，Java 中是 8 个字节;
3. c99标准下，long类型的定义为：不可以比整形小；
4. C语言中 boolean 表示：0（flase）,非0(true)；
5. C语言中没有 byte 类型；
6. unsigned 无符号 0~255；
7. signed 有符号 –128~127；
8. void 无类型，任意类型；



### 输入输出函数

#### 占位符

```c
%d  –  int
%ld –  long int
%c  –  char
%f  –  float
%u  –  无符号数
%hd –  短整型
%lf –  double
%x  –  十六进制输出爄nt 或者long int 或者short int
%o  –  八进制输出
%s  –  字符串
```



#### 输入函数

```c
#include<stdio.h>
#include<stdlib.h>
/**
输入函数 scanf("占位符"，内地地址) 
*/ 

main(){
    int i ;
    printf("请输入一个你认为最帅的数字：\n"); 
    scanf("%d",&i);
    printf("你输入的数字是：%d\n",i); 

    //输入 
    char cArray[] = {'H','E','L','L','O'}; 
    //在C语言中没有String 类型，但是可以用char数组来表示 
    int j;
    for( j=0;j<5;j++){

        printf("cArray[%d]==%c\n",j,cArray[j]);    

    }
    printf("cArray==%s\n",cArray); 

    system("pause");
}
```



#### 输出函数

```java
#include<stdio.h>
#include<stdlib.h>

/**
输出函数 printf("输出的内容对应的占位符");

在C语言中，默认保留小数点后六位 
要想保留对应的位数，就需要在百分号后边加上“.数字” 
 
十进制：12345678
二进制：1011 1100 0110 0001 0100 1110 
                  110 0001 0100 1110

不同的类型，要用不同的占位去输出，否则精度丢失。 
*/ 

main() {
       char c = 'A';
       int i = 12345678;
       long l = 123456789;
       float f = 3.1415;
       double d = 3.1415926535;
       
       printf("c==%c\n",c); 
       printf("i==%d\n",i); 
       printf("l==%ld\n",l); 
       printf("f==%.4f\n",f); 
       printf("d==%.10lf\n",d); 
       printf("i==%hd\n",i); 
       //C语言的数组的括号不能写在左边 
       char cArray[]   = {'A','B'};
       printf("cArray内存地址==%#x\n",&cArray); 
       
       char* text = "I love you!";
       printf("text内容==%s\n",text); 
       
       system("pause");
}
```





### 指针

```c
#include<stdio.h>
#include<stdlib.h>

/**
 指针就是内存地址
 内地地址就是指针 
*/

main(){
   //定义一个int类型的变量i,并且赋值为10； 
   int i = 10;
   //定义一个int类型的一级指针变量p 
   int* p; 
   //把i对应的地址赋值给p变量    
   p = &i; 
   
   //指针取值*p :把p变量对应的地址的值取出来 
   printf("*p===%d\n",*p); 
   *p = 100;//赋值 
   printf("*p===%d\n",*p); 
       
  system("pause");     
}     

```



#### 两数交换

```c
void sitch2(int* a,int* b){//传地址可以改变值 
   int temp = *a;
   *a = *b;
   *b = temp; 
   printf("sitch 中a地址===%#x\n",a); 
   printf("sitch 中b地址===%#x\n",b); 
}

main(){
   int a = 100;
   int b = 200;
   printf("main中a地址===%#x\n",&a); 
   printf("main中b地址===%#x\n",&b); 
   printf("a===%d\n",a); 
   printf("b===%d\n",b); 
   sitch2(&a,&b);
   printf("a===%d\n",a); 
   printf("b===%d\n",b); 
}
```



#### 指针和指针变量

1. 指针就是地址，地址就是指针，地址就是内存单元的编号；

2. 指针变量是存放地址的变量；

3. 指针和指针变量是两个不同的概， 通常我们叙述时会把指针变量简称为指针，实际它们含义并不一样，如：

   - 指针里存的是100,  指针: 地址--具体

   - 指针里存的是地址, 指针: 指针变量 -- 可变



#### 指针的优点

指针能够直接访问硬件和快速传递数据，能够表示复杂的数据结构，方便处理字符串，指针有助于理解面向对象



#### *号的含义 

1. 数学运算符:  3 * 5 
2. 定义指针变量:  int*  p；
3. 指针运算符(取值):  *p (取p的内容(地址)在内存中的值)



#### 多级指针

```c
#include<stdio.h>
#include<stdlib.h>
/**
 多级指针 
 指针指向的是内存地址
 地址就是指针 
*/
main(){
  //定义一个int类型的变量i,并且赋值为100； 
  int i = 100;  
  //定义一个int类型的一级指针变量address1,并且把i的地址赋值给它 
  int* address1 = &i;
  //定义一个int类型的二级指针变量 address2，并且把 address1对应的地址赋值给它 
  int** address2 = &address1;
  //定义三级指针 
  int*** address3 =  &address2;
  //定义四级指针 
 // int**** address4 =  &address3;
  
  //多级指针取值 ****address4得到的值是100
  printf("***address3==%d\n",***address3); 
  //*address4
  ***address3 = 2000;
  printf("***address3==%d\n",***address3); 
       
  system("pause");      
} 
```



### 数组

```c
#include<stdio.h>
#include<stdlib.h>

/**
 数组的介绍
 1.数组的取值
 2.数组的取地址
 3.数组是一块连续的内地空间
 4.数组的首元素的首地址和数组的地址相同 
 4.数组的设计
 */
main(){
    char cArray[] = {'H','E','L','L','O'};
    int iArray[] = {1,2,3,4,5}  ;
    //取数组的值
    printf("cArray[0]==%c\n",cArray[0]);
    printf("cArray[1]==%c\n",cArray[1]);

    printf("iArray[0]==%d\n",iArray[0]);
    printf("iArray[1]==%d\n",iArray[1]);

    //取内存地址值 
    printf("cArray地址==%#x\n",&cArray);
    printf("cArray[0]地址==%#x\n",&cArray[0]);
    printf("cArray[1]地址==%#x\n",&cArray[1]);
    printf("cArray[2]地址==%#x\n",&cArray[2]);

    printf("cArray地址==%#x\n",cArray);
    printf("cArray+0地址==%#x\n",cArray+0);
    printf("cArray+1地址==%#x\n", cArray+1);
    printf("cArray+2地址==%#x\n", cArray+2);

    printf("iArray + 0==%#x\n",iArray+0);
    printf("iArray + 1==%#x\n",iArray+1);
    printf("iArray + 2==%#x\n",iArray+2);
    printf("iArray + 3==%#x\n",iArray+3);

    //内存是一块连续的内存空间 
    printf("iArray地址==%#x\n",&iArray);
    printf("iArray[0]地址==%#x\n",&iArray[0]);
    printf("iArray[1]地址==%#x\n",&iArray[1]);
    printf("iArray[2]地址==%#x\n",&iArray[2]);
    printf("iArray[3]地址==%#x\n",&iArray[3]);

    //用指针取值
    printf("iArray==%d\n",*iArray);
    printf("iArray[0]==%d\n",*iArray+0);
    printf("iArray[1]==%d\n",*iArray+1);
    printf("iArray[2]==%d\n",*iArray+2);
    printf("iArray[3]==%d\n",*iArray+3);

    printf("iArray[0]==%d\n",*(iArray+0));
    printf("iArray[1]==%d\n",*(iArray+1));
    printf("iArray[2]==%d\n",*(iArray+2));
    printf("iArray[3]==%d\n",*(iArray+3));

    system("pause");
}
```



### 内存分配

#### 动态内存和静态内存

静态内存是程序编译执行后系统自动分配,由系统自动释放, 静态内存是栈分配的。动态内存是开发者手动分配的, 是堆分配的。

1. 从静态存储区域分配。内存在程序编译的时候就已经分配好，这块内存在程序的整个运行期间都存在。例如全局变量，static 变量。
2. 在栈上创建。在执行函数时，函数内局部变量的存储单元都可以在栈上创建，函数执行结束时这些存储单元自动被释放。栈内存分配运算内置于处理器的指令集中，效率很高，但是分配的内存容量有限。
3. 从堆上分配，亦称动态内存分配。程序在运行的时候用malloc 或new 申请任意多少的内存，程序员自己负责在何时用free 或delete 释放内存。动态内存的生存期由我们决定，使用非常灵活，但问题也最多.

**堆和栈的区别**

1. 申请方式
   - 栈：由系统自动分配.例如,声明一个局部变量int  b; 系统自动在栈中为 b 开辟空间。例如当在调用涵数时，需要保存的变量，最明显的是在递归调用时，要系统自动分配一个栈的空间，后进先出的，而后又由系统释放这个空间。
   - 堆：需要程序员自己申请，并指明大小，在 c 中用 malloc 函数，如`char*  p1  =  (char*) malloc(10);   //14byte`，但是注意p1本身是在栈中的。
2. 申请后系统的响应
   - 栈：只要栈的剩余空间大于所申请空间，系统将为程序提供内存，否则将报异常提示栈溢出。
   - 堆：首先应该知道操作系统有一个记录空闲内存地址的链表，当系统收到程序的申请时，会遍历该链表，寻找第一个空间大于所申请空间的堆结点，然后将该结点从空闲结点链表中删除，并将该结点的空间分配给程序，另外，对于大多数系统，会在这块内存空间中的首地址处记录本次分配的大小，这样，代码中的 delete 语句才能正确的释放本内存空间。另外，由于找到的堆结点的大小不一定正好等于申请的大小，系统会自动的将多余的那部分重新放入空闲链表中。

3. 申请大小的限制   
   - 栈：在Windows下,栈是向低地址扩展的数据结构，是一块连续的内存的区域。这句话的意思是栈顶的地址和栈的最大容量是系统预先规定好的，在 Windows下，栈的大小是2M（vc 编译选项中可以设置,其实就是一个STACK参数,缺省2M），如果申请的空间超过栈的剩余空间时，将提示 overflow。因此，能从栈获得的空间较小。
   - 堆：堆是向高地址扩展的数据结构，是不连续的内存区域。这是由于系统是用链表来存储的空闲内存地址的，自然是不连续的，而链表的遍历方向是由低地址向高地址。堆的大小受限于计算机系统中有效的虚拟内存。由此可见，堆获得的空间比较灵活，也比较大。 

4. 申请效率的比较
   - 栈:由系统自动分配，速度较快。但程序员是无法控制的。
   - 堆:由 malloc/new 分配的内存，一般速度比较慢，而且容易产生内存碎片,不过用起来最方便.   

5. 堆和栈中的存储内容 
   - 栈:在函数调用时，第一个进栈的是主函数中后的下一条指令（函数调用语句的下一条可执行语句）的地址，然后是函数的各个参数，在大多数的 C 编译器中，参数是由右往左入栈的，然后是函数中的局部变量。注意静态变量是不入栈的。当本次函数调用结束后，局部变量先出栈，然后是参数，最后栈顶指针指向最开始存的地址，也就是主函数中的下一条指令，程序由该点继续运行。
   - 堆：一般是在堆的头部用一个字节存放堆的大小。堆中的具体内容有程序员安排。   

6. 内存的回收

   栈上分配的内存，编译器会自动收回；堆上分配的内存，要通过free来显式地收回,否则会造成内存泄漏。

```c
#include<stdio.h>
#include<stdlib.h>
#include <malloc.h>  //不能省略
//malloc是memory(内存) allocate(分配)的缩写

int main(void) {
  	int i = 5; 					   //11行 分配了4个字节 静态分配
    /*
       1. 要使用malloc函数，必须添加malloc.h这个头文件
       2. malloc函数只有一个形参，并且形参是整型
       3. 4表示请求系统为本程序分配4个字节
       4. malloc函数只能返回第一个字节的地址
       5. 12行分配了8个字节, p变量占4个字节， p所指向的内存也占4个字节
       6. p本身所占的内存是静态分配的， p所指向的内存是动态分配的    
     */
  	int* p = (int *)malloc(4); 		//12行
     
  	*p = 5;  //*p 表示一个int变量， 只不过*p这个整型变量的内存分配方式和11行的i变量的分配方式不同
  	free(p); //freep(p)表示把p所指向的内存给释放掉  
    // p本身的内存是静态的，不能由程序员手动释放，p本身的内存只能在p变量所在的函数运行终止时由系统自动释放 
  	printf("大家好！\n");
  	return 0;
}
```



#### 静态内存分配

```java
#include<stdio.h>
#include<stdlib.h>

void func(int** address){
    //定义int类型的i变量，并且赋值100 
    int i = 100;
    // 把i对应的地址赋值给 iPoint变量 
    *address = &i; 
}

main(){
    //定义int类型的一级指针变量 iPoint
    int* iPoint; 
    func(&iPoint);
    
    printf("*iPoint===%d\n",*iPoint);
    printf("*iPoint===%d\n",*iPoint);
    printf("*iPoint===%d\n",*iPoint);
    
    system("pause");   
}       
```



#### 动态内存分配

特点：申请完之后，只要不回收就会一直在内存中存在子函数的值，可以被主函数长时间的保留。

```c
#include<stdio.h>
#include<stdlib.h>

void func(int** address){
    int i = 100;
    int* temp;
    //malloc(int)-内存地址 
    temp = malloc(sizeof(int)); 
    //把i对应的值，赋值给 temp地址对应的值 
    *temp = i;
    //把address对应的地址对应的值修改成 temp
    *address = temp;
     
    free(temp); 
}

main(){
      //定义int类型的一级指针变量 iPoint
     int* iPoint; 
     func(&iPoint); 
    
     printf("*iPoint===%d\n",*iPoint);
     printf("*iPoint===%d\n",*iPoint);
     printf("*iPoint===%d\n",*iPoint);
     printf("*iPoint===%d\n",*iPoint);
     system("pause");   
}
```



### 动态创建数组

```c
#include<stdio.h>
#include<stdlib.h>

/**
 动态创建数组 
 输出函数 printf(); 
 输入函数：scanf("占位符"，内存地址); 
 realloc()重新分配内存 
*/

main() {
    // 动态数组的创建
    printf("请输入您要创建数组的长度:\n");
    //1、让用户输入一个长度
    int length;
    scanf("%d",&length);
    printf("您输入数组的长度为:%d\n",length);
    
    //2、根据长度，分配内存空间
    int* iArray = malloc(length*4);
    
    //3、让用户把数组中的元素依次的赋值；
    int i;
    for(i=0;i<length;i++){
       printf("请输入iArray[%d]的值：",i);   
       scanf("%d",iArray+i);                
    }
    
    //4、接收用户输入扩展数组长度
    int suppLength;
    printf("请输入您要扩展数组的长度:\n");
    scanf("%d",&suppLength); 
    printf("您要扩展数组的长度:%d\n",suppLength);
    
    //5、根据扩展的长度重新分配空间?
    //realloc
    iArray = realloc(iArray,(length+suppLength)*4);
    
    //6、把扩展长度的元素让用户赋值；
    for(i=length;i<length+suppLength;i++){
       printf("请输入iArray[%d]的值：",i);   
       scanf("%d",iArray+i);                
    }
    
    //7、输出数组
    for(i=0;i<length+suppLength;i++){
       printf("iArray[%d]==%d\n",i,*(iArray+i));   

    }
    
    system("pause");   
}
```



### 其他

#### Typedef

Typedef 用于声明自定义数据类型，配合各种原有数据类型来达到简化编程的目的的类型定义。可以在类中把名字很长的方法用简写或者代替方式。

```c
#include <stdio.h> 
#include <stdlib.h>
typedef int i;
typedef long l;
typedef float f;

main() {
    i m = 10;
    l n = 123123123;
    f s = 3.1415;
    printf("%d\n", m);
    printf("%ld\n", n);
    printf("%f\n", s);
    
    system("pause");       
}

```



#### 结构体

```c
#include<stdio.h>
#include<stdlib.h>

//定义结构
struct student{
    int age;//4个字节 
    float score;//4个字节 
    char sex;   //1个字节 
}; //; 不能省略

main(){
    //使用结构图
    struct student stu = {18,98.9,'W'};
    //结构体取值 
    printf("stu.age==%d\n",stu.age);
    printf("stu.score==%.1f\n",stu.score);
    printf("stu.sex==%c\n",stu.sex);
    //结构体赋值
    stu.age = 20;
    stu.score = 100;
    stu.sex = 'M';
    printf("stu.age==%d\n",stu.age);
    printf("stu.score==%.1f\n",stu.score);
    printf("stu.sex==%c\n",stu.sex);
    //结构体的长度 
    printf("struct student长度==%d\n",sizeof(struct student));
    
    system("pause"); 
}

```



#### 联合体

```c
#include<stdio.h>
#include<stdlib.h>

//定义一个联合体，特点，所有的字段都是使用同一块内存空间； 
union Mix {
     long i; //4个字节 
     int k; //4个字节 
     char ii;//1个字节 
};

main() { 
    printf("mix:%d\n",sizeof(union Mix)); 
    //实验 
    union Mix m;
    m.k = 123;
    m.i = 100;
    printf("m.i=%d\n",m.i);         
    printf("m.k=%d\n",m.k);  
    
    system("pause");
} 

```



#### 枚举

```c
#include<stdio.h>
#include<stdlib.h>

/**
枚举的值递增 默认首元素的值是0 
*/
enum WeekDay {
     Monday,Tuesday,Wednesday,Thursday,Friday,Saturday,Sunday
};
 
main() {
       enum WeekDay day = Wednesday;
       printf("%d\n",day);
    
       system("pause");
}
```



#### 函数指针

```c
#include<stdio.h>
#include<stdlib.h>

//定义一个函数 
int add(int x,int y) {
    return x + y;
} 

main(){
     //定义函数指针
     int (*android)(int x,int y); 
     //函数指针赋值
     android = add;
     //使用函数指针 
     int result = add(99,1);
     printf("result==%d\n",result);

     system("pause");   
}

```



#### 结构体指针

```c
#include<stdio.h>
#include<stdlib.h>

//定义结构
struct student{
    int age;//4个字节 
    float score;//4个字节 
    char sex;   //1个字节 
} ;  

main(){
    //使用结构图
    struct student stu = {18,98.9,'W'};

    //结构体指针
    struct student* point = &stu;

    struct student** point2 = &point;

    //取值运算(*point).age  等价于 point->age  
    printf("(*point).age ==%d\n",(*point).age ); 
    printf("point->age ==%d\n",point->age ); 
    printf("point->score ==%f\n",point->score ); 
    printf("point->sex ==%c\n",point->sex ); 
    //赋值运算
    point->age = 20; 
    point->score = 100;
    point->sex = 'M';
    printf("point->age ==%d\n",point->age ); 
    printf("point->age ==%d\n",point->age ); 
    printf("point->score ==%f\n",point->score ); 
    printf("point->sex ==%c\n",point->sex );  

    //二级结构体指针取值 (*point).age  等价于 point->age   所以  (**point).age 等价于 (*point)->age
    printf("(**point2).age ==%d\n",(**point2).age ); 
    printf("(*point2)->age ==%d\n",(*point2)->age ); 
    //二级指针赋值
    (**point2).age = 2000;
    printf("(**point2).age ==%d\n",(**point2).age ); 
    printf("(*point2)->age ==%d\n",(*point2)->age ); 
    system("pause"); 
}
```



## JNI开发

### NDK集成开发流程

NDK(native develop kits)：可以实现不同平台下(windows)编译出另一个平台下的可执行二进制文件。

配置步骤：

1. 下载：[Windows 64-bit 版本下载](http://dl.google.com/android/ndk/android-ndk-r9-windows-x86_64.zip)，解压到非中文目录下，配置环境变量

> 默认安装目录：\AndroidTools\AndroidSDK\ndk-bundle\

2. 给AS配置关联NDK

   - local.properties中添加配置 `ndk.dir=D\:\\AndroidTools\\AndroidSDK\\ndk-bundle`

   - gradle.properties中添加配置`android.useDeprecatedNdk=true

3. 编写native方法

   ```java
   public class JNIS {
     	public native String helloJNI();
   }
   ```

4. 定义对应的JNI

   - 在main下创建 jni 文件夹
   - 生成 native 方法对应的 JNI 函数声明头文件：命令窗口中, 进入 java 文件夹

   ```shell
   #执行命令
   javah com.xiaobai.NDKHelloWorld.JNIS
   #生成头文件
   com_xiaobai_NDKHelloWorld_JNIS.h
   #函数声明
   JNIEXPORT jstring JNICALL Java_com_xiaobai_NDKHelloWorld_JNIS_helloJNI(JNIEnv *, jobject);
   ```

   - 将生成的头文件转移到jni文件夹下
   - 在 jni下定义对应的函数文件: test.c

   ```c
   #include "com_xiaobai_NDKHelloWorld_JNIS.h"
   JNIEXPORT jstring JNICALL Java_com_xiaobai_NDKHelloWorld_JNIS_helloJNI
       (JNIEnv * env, jobject jobj) {
       return (*env)->NewStringUTF(env, "Hello from C");
   }
   ```

   - 在jni文件夹下创建一个空的C文件: empty.c

     > 说明: 这是AS的bug, 必须至少2个C文件才能通过编译

5. 指定编译的不同CPU

   ```groovy
   defaultConfig {
       ndk{
           moduleName "HelloJni" //so文件: lib+moduleName+.so
           abiFilters "armeabi", "armeabi-v7a", "x86" //cpu的类型
       }
   }
   ```

6. 编译生成不同平台下的动态链接文件
   - 执行 rebuild，生成 so 文件
   - so 文件目录：build\intermediates\ndk\debug\lib\.....

7. 调用native方法

   - 在native方法所在的类中加载 so 文件

   ```java
   static {
      System.loadLibrary("HelloJni");
   }
   ```

   - 在Activity中调用 native方法

   ```java
   String result = new JNIS().helloJNI();
   Log.e("TAG", "result="+result);
   ```

> 实践过程发现以上配置过程已过时，无法与编译生产ndk目录。新版采用 cmake 方式构建项目，详情参考博客：
>
> [超详细的Android NDK开发环境搭建](https://blog.csdn.net/android_cai_niao/article/details/106474705)



### 详解NDK开发

#### 开发步骤

1. 进入`main`目录下执行`javah <全类名>` 命令生成`.h`文件；
2. 在`main`目录下添加`jni`目录，将生成的`.h`文件移入；
3. 创建`c`文件并引入生成的`.h`文件，编写`JNI`实现；
4. 添加`cMakeList.txt`文件

```tex
# 设置构建native library所需的CMake最低版本。
cmake_minimum_required(VERSION 3.4.1)

#创建一个库（多次调用add_library即可创建多个库）
add_library( # 设置库的名称
        libname

        # 将库设置为共享库（即so文件）
        SHARED

        # 指定源文件的相对路径
        source_path )

```

5. 在`.gradle`文件中添加配置

```groovy
android {
	externalNativeBuild {
        cmake {
            path file('src/main/jni/CMakeLists.txt')
        }
    }

    sourceSets.main {
        //让AS识别libs下的.so第三方包
        jniLibs.srcDirs = ['libs']
    }
}
```



#### Java调用C函数

JNI

```java
public class JNIS {
    //两数相加
    public native int sum(int x, int y);
    //字符串拼接
    public native String sayHello(String s);
    //数组运算
    public native int[] incArrElms(int[] intArray);
    //检查密码
    public native int checkPwd(String pwd);
}
```

JNI实现

```c
/**
 * 把一个jstring转换成一个c语言的char* 类型.
 */
char* _JString2CStr(JNIEnv* env, jstring jstr) {
    char* rtn = NULL;
    jclass clsstring = (*env)->FindClass(env, "java/lang/String");
    jstring strencode = (*env)->NewStringUTF(env,"GB2312");
    jmethodID mid = (*env)->GetMethodID(env, clsstring, "getBytes", "(Ljava/lang/String;)[B");
    jbyteArray barr = (jbyteArray)(*env)->CallObjectMethod(env, jstr, mid, strencode); // String .getByte("GB2312");
    jsize alen = (*env)->GetArrayLength(env, barr);
    jbyte* ba = (*env)->GetByteArrayElements(env, barr, JNI_FALSE);
    if(alen > 0) {
        rtn = (char*)malloc(alen+1); //"\0"
        memcpy(rtn, ba, alen);
        rtn[alen]=0;
    }
    (*env)->ReleaseByteArrayElements(env, barr, ba,0);
    return rtn;
}

JNIEXPORT jint JNICALL Java_com_xiaobai_javac_JNIS_sum(JNIEnv *env, jobject thiz, jint x, jint y) {
    // TODO: implement sum()
    int result = x +y;
    return result;
}

JNIEXPORT jstring JNICALL Java_com_xiaobai_javac_JNIS_sayHello(JNIEnv *env, jobject thiz, jstring s) {
    // TODO: implement sayHello()
    char* fromJava = _JString2CStr(env,s);
    char* fromC = "Hello";
    strcat(fromJava,fromC);
    jstring result = (*env)->NewStringUTF(env,fromJava);
    return result;
}


JNIEXPORT jintArray JNICALL Java_com_xiaobai_javac_JNIS_incArrElms(JNIEnv *env, jobject thiz, jintArray int_array) {
    // TODO: implement incArrElms()
    //获取数组长度
    int size = (*env)->GetArrayLength(env,int_array);
    //得到数组元素
    jint* arr = (*env)->GetIntArrayElements(env,int_array,JNI_FALSE);
    //遍历数组
    for(int i=0;i<size;i++){
        *(arr+i)=*(arr+i)+10;
        *(arr+i)+=10;
    }
    return arr;
}


JNIEXPORT jint JNICALL Java_com_xiaobai_javac_JNIS_checkPwd(JNIEnv *env, jobject thiz, jstring pwd) {
    // TODO: implement checkPwd()
    char* origin = "123456";
    char* target = _JString2CStr(env,pwd);
    int code = strcmp(origin,target);
    if (code==0){
        return 200;
    }else{
        return 400;
    }
}

```



#### C回调Java方法

1. C回调Java方法的核心思想: 反射

2. 如何得到一个方法的签名?
   - 在命令行窗口中, 进入应用的 javac/debug/ 目录
   - 执行命令: javap -s 全类名, 显示所有方法的签名信息

JNI

```java
public class JNI {
    {
        System.loadLibrary("ccalljava");
    }

    /**
     * 当执行这个方法的时候，让C代码调用
     * public int add(int x, int y)
     */
    public native void callbackAdd();

    /**
     * 当执行这个方法的时候，让C代码调用
     * public void helloFromJava()
     */
    public native void callbackHelloFromJava();


    /**
     * 当执行这个方法的时候，让C代码调用void printString(String s)
     */
    public native void callbackPrintString();

    /**
     * 当执行这个方法的时候，让C代码静态方法 static void sayHello(String s)
     */
    public native void callbackSayHello();


    public int add(int x, int y) {
        Log.e("TAG", "add() x=" + x + " y=" + y);
        return x + y;
    }

    public void helloFromJava() {
        Log.e("TAG", "helloFromJava()");
    }

    public void printString(String s) {
        Log.e("TAG","C中输入的：" + s);
    }

    public static void sayHello(String s){
        Log.e("TAG",  "我是java代码中的JNI."
                + "java中的sayHello(String s)静态方法，我被C调用了:"+ s);
    }
}
```



MainActivity

```java
public class MainActivity extends AppCompatActivity {

    private JNI jni;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        jni = new JNI();
    }

    //显示Toast
    public native void callbackShowToast();

    public void showToast(){
        Log.e("Tag","showToast");
        Toast.makeText(this,"result==ok",Toast.LENGTH_SHORT).show();
    }

}
```



JNI实现

```c

/**
 * 把一个jstring转换成一个c语言的char* 类型.
 */
char* _JString2CStr(JNIEnv* env, jstring jstr) {
    char* rtn = NULL;
    jclass clsstring = (*env)->FindClass(env, "java/lang/String");
    jstring strencode = (*env)->NewStringUTF(env,"GB2312");
    jmethodID mid = (*env)->GetMethodID(env, clsstring, "getBytes", "(Ljava/lang/String;)[B");
    jbyteArray barr = (jbyteArray)(*env)->CallObjectMethod(env, jstr, mid, strencode); // String .getByte("GB2312");
    jsize alen = (*env)->GetArrayLength(env, barr);
    jbyte* ba = (*env)->GetByteArrayElements(env, barr, JNI_FALSE);
    if(alen > 0) {
        rtn = (char*)malloc(alen+1); //"\0"
        memcpy(rtn, ba, alen);
        rtn[alen]=0;
    }
    (*env)->ReleaseByteArrayElements(env, barr, ba,0);
    return rtn;
}


JNIEXPORT void JNICALL
Java_com_xiaobai_cjava_JNI_callbackAdd(JNIEnv *env, jobject thiz) {
    //得到字节码
    jclass jclazz = (*env)->FindClass(env,"com/xiaobai/cjava/JNI");
    //获取调用方法签名
    jmethodID  jmethodId = (*env)->GetMethodID(env,jclazz,"add","(II)I");
    //获取Java实例
    jobject jobject = (*env)->AllocObject(env,jclazz);
    //调用方法
    (*env)->CallIntMethod(env,jobject, jmethodId,100,100);
}

JNIEXPORT void JNICALL
Java_com_xiaobai_cjava_JNI_callbackHelloFromJava(JNIEnv *env, jobject thiz) {
    // 获取字节码
    jclass jclazz = (*env)->FindClass(env,"com/xiaobai/cjava/JNI");
    //获取调用方法签名
    jmethodID  jmethodId = (*env)->GetMethodID(env,jclazz,"helloFromJava","()V");
    //获取Java实例
    jobject jobject = (*env)->AllocObject(env,jclazz);
    //调用方法
    (*env)->CallVoidMethod(env,jobject,jmethodId);
}

JNIEXPORT void JNICALL
Java_com_xiaobai_cjava_JNI_callbackPrintString(JNIEnv *env, jobject thiz) {
    // 获取字节码
    jclass jclazz = (*env)->FindClass(env,"com/xiaobai/cjava/JNI");
    //获取调用方法签名
    jmethodID  jmethodId = (*env)->GetMethodID(env,jclazz,"printString","(Ljava/lang/String;)V");
    //获取Java实例
    jobject jobject = (*env)->AllocObject(env,jclazz);
    //调用方法
    jstring jstring = (*env)->NewStringUTF(env,"I am from c");
    (*env)->CallVoidMethod(env,jobject,jmethodId,jstring);
}

JNIEXPORT void JNICALL
Java_com_xiaobai_cjava_JNI_callbackSayHello(JNIEnv *env, jobject thiz) {
    // 获取字节码
    jclass jclazz = (*env)->FindClass(env,"com/xiaobai/cjava/JNI");
    //获取调用方法签名
    jmethodID  jmethodId = (*env)->GetStaticMethodID(env,jclazz,"sayHello","(Ljava/lang/String;)V");
    //调用方法
    jstring jstring = (*env)->NewStringUTF(env,"I am from android c");
    (*env)->CallStaticVoidMethod(env,jclazz,jmethodId,jstring);
}


JNIEXPORT void JNICALL
Java_com_xiaobai_cjava_MainActivity_callbackShowToast(JNIEnv *env, jobject thiz) {
    // 获取字节码
    jclass jclazz = (*env)->FindClass(env,"com/xiaobai/cjava/MainActivity");
    //获取调用方法签名
    jmethodID  jmethodId = (*env)->GetMethodID(env,jclazz,"showToast","()V");
    //调用方法
    (*env)->CallVoidMethod(env,thiz,jmethodId);
}
```



#### JNI打印日志

1. 在 CMakeLists.txt 文件中引用添加原生 NDK Log 包；

```tex
find_library( # Defines the name of the path variable that stores the
        # location of the NDK library.
        log

        # Specifies the name of the NDK library that
        # CMake needs to locate.
        log )
target_link_libraries( # Specifies the target library.
        jni-lib

        # Links the log library to the target library.
        ${log} )
```

2. JNI 实现文件引用

```c
#include <android/log.h>
#define LOG_TAG "jni-name"
#define LOGI(...)  __android_log_print(ANDROID_LOG_DEBUG,LOG_TAG,__VA_ARGS__)
#define LOGI(...)  __android_log_print(ANDROID_LOG_INFO,LOG_TAG,__VA_ARGS__)
#define LOGE(...)  __android_log_print(ANDROID_LOG_ERROR,LOG_TAG,__VA_ARGS__)

```



更多详情内容，请参考：[NDK开发者文档](https://developer.android.google.cn/ndk)

其他参考博客：[Android系统的JNI原理分析（五）- JNI函数解析](https://blog.csdn.net/Xiaoma_Pedro/article/details/112297685)