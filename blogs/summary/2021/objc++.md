---
title: C++ 类与对象的创建和使用
date: 2021-11-24
tags:
 - C++
categories:
 - 学习总结
---

#### 前言

笔者在大一大二期间学过 C 和 C++，当时对编程没有太多理解，加上没有经过大量的代码练习，所以仅凭借记忆吸收的一点点知识都还给老师了。由于选修了 Visual C++ 课程，加上最近在刷算法，感觉还是有必要把之前学过的 C++ 语言基础补起来。时隔两年重新翻看《C++语言程序设计》这本书，真后悔自己之前把这么好的一本教材给冷落了。

以下内容是我结合教程例 4-4 ，从代码层面上对第四章涉及到的基本专业术语及类和对象的实现过程的进行的总结。笔者主要使用 Java 语言，对 Java 语言的特性以及面向对象编程有一定理解，所以以下内容都相对精简，可能不适合小白食用。详细内容可以翻阅清华大学计算机系列教材《C++ 语言程序设计 (第四版)》第四章。

**源码如下：**

```c++
#include <iostream>
using namespace std;

//前项引用声明
//C++类应先定义再使用，但在复杂场景中可能出现循环依赖的情况，这时可以使用前向声明
//在提供一个完整的类定义之前，不能定义该类的对象，不能在内联成员中使用该类对象
//class Point;
// 
//class 类名
class Point {//点类的定义

public://外部接口
	//默认构造函数，未定义有参构造函数时系统默认生成，对象创建时自动调用
	Point() {};

	//有参构造函数，将对象初识化为特定状态，可以是内联函数
	Point(int xx = 0, int yy = 0) {
		x = xx;
		y = yy;
		cout << "Calling the copyh constructor of Point" << endl;
	}

	//复制构造函数，用一个已存在的对象初识化另一个同类新对象，形参是本类对象的引用
	Point(Point& p);

	//析构函数，有始有终，在对象生命周期即将结束时自动调用，不接收任何参数
	~Point() {};

	//内联函数，编译时将代码嵌入调用处，提高执行效率。
	//我们将频繁调用且代码简单的成员函数声明为内联函数，声明方式分为显式和隐式

	//显式内联函数：方法实现在类外边
	inline int getY();
	//隐式内联函数：方法实现在类里边
	int getX() { return x; }


private://私有成员
	int x, y;
};

//成员函数实现：
//返回类型 返回类名::方法名（参数列表）
Point::Point(Point& p) 
{
	x = p.x;
	y = p.y;
}
//返回类型 返回类名::方法名（参数列表）
int Point::getY() 
{ 
	return y; 
}


//组合类
class Line {//Line类的定义

public:
	//构造函数
	Line(Point xp1, Point xp2);
	//复制构造函数
	Line(Line& l);
	//析构函数
	~Line() {};
	//内联函数
	double getLen() { return len; }

private://私有成员
	Point p1, p2;
	double len;
};

//组合类构造函数实现：
//类名::类名(形参表):内嵌对象1(形参表):内嵌对象2(形参表)
Line::Line(Point xp1, Point xp2) :p1(xp1), p2(xp2)
{
	cout << "Calling constructor of Line" << endl;
	double x = static_cast<double>(p1.getX() - p2.getX());
	double y = static_cast<double>(p1.getY() - p2.getY());
	len = sqrt(x * x + y * y);
}

Line::Line(Line& l) :p1(l.p1), p2(l.p2)
{
	cout << "Calling constructor of Line" << endl;
	len = l.len;
}


int main() {
	Point p1(1, 1), p2(4, 5);
	Line line1(p1, p2);
	Line line2(line1);
	cout << "The length of the line1 is:" << line1.getLen() << endl;
	cout << "The length of the line2 is:" << line1.getLen() << endl;
	return 0;
}
```

**输出结果：**

![image-20211124133207362](http://image.xiaobailx.top/images/20211124133311.png)

