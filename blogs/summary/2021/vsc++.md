---
title: 使用VS打开VC++6.0创建的MFC项目
date: 2021-11-23
tags:
 - C++
categories:
 - 踩坑
---

大一的我年少无知，学 C 语言时老师叫我们每位同学学习使用 VC++6.0，就此成为了经典传承人之一。突然有一天，我发现我再也无法忍受它没有一点现代美感且过气的界面，由此开始了我寻找最美编译器之旅，与此同时也揭开了我所学的计算机专业的真相——严重与社会需求脱节。大四为了凑够学分，选修了 Visual C++，学院教授在讲台上侃侃而谈，熟练地操作的经典永流传的 VC++6.0。我平静地坐在台下，内心毫无波澜。


故事讲完了，开始干活了。需要说明的是，笔者使用的 VS 是 2019 版本， 且已安装了运行 MFC 项目的环境。VS2017 ~ VS2019 理论上不会有兼容性问题。开始正题，使用 VS2019 打开原本 VC++6.0 创建的 MFC 项目，导入项目后 VS2019 会出现如下提示：



![image-20211123141214451](http://image.xiaobailx.top/images/20211123141216.png)



点击确定，转换并导入成功后，点击 Debug 进行调试，会出现错误提示，如下图：

![image-20211123150441662](http://image.xiaobailx.top/images/20211123150441.png)



**解决方案：**

1. 点击 【项目】=》【属性】=》【C/C++】=》【启用函数集链接】=》选择【是 (/Gy)】

![image-20211123150640069](http://image.xiaobailx.top/images/20211123150640.png)



2. 点击 【项目】=》【属性】=》【C/C++】=》【常规】=》【调试信息格式】=》选择【程序数据库(/Zi)】或【无】

![image-20211123151236366](http://image.xiaobailx.top/images/20211123151236.png)



点击【应用】后，再次 Debug 调试运行，出现新错误，如下：

![image-20211123151650364](http://image.xiaobailx.top/images/20211123151650.png)



**解决方案：**

![image-20211123152236594](http://image.xiaobailx.top/images/20211123152236.png)



以上两个错误是最基本的 VC++6.0 转到 VS2019 时遇到的基本错误，根据项目的源码 不同，可能还出现以下情况，都是我在 Copy 代码时遇到的，具体如下：

- 运算符不匹配，原因是 CString 在 VS 和 VC++6.0 两个环境下的解码方式不同。

![image-20211123214735922](http://image.xiaobailx.top/images/20211123214736.png)



**解决方案：**点击 【项目】=》【属性】=》【高级】=》【字符集】=》选择【未设置)】或【使用多字节字符集】

![image-20211123220408814](http://image.xiaobailx.top/images/20211123220408.png)



- 读取文件时是可能会出现如下错误：

  ![image-20211123220614846](http://image.xiaobailx.top/images/20211123220614.png)



**解决方案：**

![image-20211123221455315](http://image.xiaobailx.top/images/20211123221455.png)



以上是我初次探索 MFC 项目在过程中所遇问题的总结。都快 2022 年了，还在学 MFC ，卑微 **=_=**



参考博客：[VC++6.0的MFC项目迁移到vs2019](https://blog.csdn.net/wch0001/article/details/119951556)

参考博客：[error C2593: “operator +=”不明确](https://blog.csdn.net/hxh129/article/details/9380141)

参考博客：[error C2039: "nocreate": 不是"std::basic_ios<char,std::char_traits<char>>](https://blog.csdn.net/V_zhangyang/article/details/60976235)

