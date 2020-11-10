---
layout:     post
title:      "c++指针、引用以及const"
subtitle:   "你可真是个小坏蛋"
date:       2020-11-8
author:     "Burt"
header-style: text 
tags:
    - c++
---



## 前言

c++指针不难、引用也不难、const更不难，难就难在他们组合在一起...



## 一、引用

直接用代码演示吧：

~~~c++
#include <iostream>

using namespace std;

int main()
{
	int a = 1;
	int b = 2;
	const int c = 3;
	//--------------------------------------------
	//初始化
	int &r1;		//错误，引用必须被初始化
	int &r2 = 1092;	//错误，初始化引用的值不可以是常量。解决方法：在r2前加const
	int &r3 = c;	//错误，初始化引用的值不可以是常量。解决方法：在r3前加const
	int &r4 = a;	//正确，初始化引用的值可以是变量
	int &r5 = &r4;  //错误，引用不能赋值给引用，只能赋值给指针
	
	int *t1 = &a;
	int &r6 = *t1;	//初始化引用可以用指针

	//修改值
	r4 = &b;		//错误，右边是int *类型
	r4 = *t1;		//正确，引用可直接修改
	r4 = b;			//同上
	r4 = 4;			//同上

	//输出
	cout << &r4 << endl;	//此时输出引用地址
	cout << r4 << endl;		//此时输出引用地址的值:4
	cout << a << endl;		//可以看到此时a的值已经跟随着r4改变而改变	


	return 0;
}
~~~

简单的引用使用。引用改的是值，而不是指向的变量

函数里，像我这种一开始学java的可能搞不懂c++为什么在函数里交换值不起作用，后来才知道怎么回事

~~~c++
void function(int i , int j) {

	int temp = j;
	j = i;
	i = temp;

	cout << i << endl;
	cout << j << endl;
}

int main(){
    
    int a = 1;
    int b = 2;
    
    function(a,b);		//这里输出2、1
    
    cout << a << endl;	//这两行输出1、2
	cout << b << endl;
    
    return 0;
}
~~~

这里有一个术语好像是解释这个的：[深拷贝](就是B复制A，A变B不变，就是深拷贝。再深入一点就是B申请了新内存，修改的其实是B在新内存的值 "什么是深拷贝")。如果我说的不对欢迎大佬指正。就是方法内的i和j其实新开辟了内存，存了a和b的值，然后输出i和j的值，其实跟a和b没啥关系，a和b只是进去被**Copy**的，本身的值并没改变

要想修改这个状况，只需要在形参变量名前加上引用符即可

~~~c++
void function(int &i , int &j) {

	int temp = j;
	j = i;
	i = temp;

	cout <<	i << endl;
	cout << j << endl;
}

int main(){
    
    int a = 1;
    int b = 2;
    
    function(a,b);		//这里输出2、1
    
    cout << a << endl;	//此时输出的就是修改后的a和b的内存地址的值
	cout << b << endl;	//这里输出2、1
    
    return 0;
}
~~~

利用了引用的特点。我怕你不信，我给你再弄一个

~~~c++
void test(int &i, int j) {

	cout << &i << endl;
	cout << &j << endl;
}

int main(){
    
    test(a, a);		
	cout << &a << endl;
    
}
~~~

输出结果为

0116F7E8

0116F6B4

0116F7E8



## 二、指针

上代码！

~~~c++
#include <iostream>

using namespace std;

int main()
{
	int a = 1;
	int b = 2;
	const int c = 3;
	//--------------------------------------------
	//初始化
	int *p1;			//指针可以先不初始化，但是不初始化直接用会报错，
						//造成你不知道是哪里出错
	
	int *p2 = nullptr;	//如果指针还没想好赋值，就先指向空指针，这样即使报错
						//编译器也会告诉你是空指针异常，排错范围大大减少

	int *p3 = &a;		//赋给指针的值只能是地址，即引用和指针
	int *p4 = p3;		//赋给指针的值只能是地址，即引用和指针
	int *p5 = b;		//错误，赋给指针的值不可以是变量
	int *p6 = c;		//错误，也不可以是常量
	int *p7 = 1;		//同上
    
    int *p8 , p9;		//注意这样声明只有p8是指针，而p9是int

	//修改值
	p3 = &b;			//三种修改方法：不带解引用符，赋值引用
	p3 = p4;			//不带解引用符，赋值指针
	*p3 = 1;			//带解引用符，直接修改内存中的值
	*p3 = a;
	
	//输出
	cout << p3 << endl;		//此时输出指针指向的地址
	cout << *p3 << endl;	//此时输出指针指向的变量值：1


	return 0;
}
~~~

指针第一阶段还好

第二阶段，开始出现指向指针的指针，听起来很复杂，但是其实还好

~~~c++
int **pp = &p3;			//指针pp指向指针p3

cout << pp << endl;		//指针pp的地址
cout << *pp << endl;	//指针p3的地址
cout << **pp << endl;	//指针pp指向的指针p3指向的a的值
~~~

说白了就是指向指针的指针变量

再来就是int***，这就不说了吧。。。再说下去就是套娃了

接下来再进入**函数**瞧瞧

拿引用的function函数举例子

~~~c++
void function(int *i , int *j) {

	int temp = *j;
	*j = *i;
	*i = temp;

	cout <<	*i << endl;
	cout << *j << endl;
}

int main(){
    int a = 1;
    int b = 2;
    
    function(&a , &b);	//这里输出2、1
    
    cout << a << endl;	//这里输出2、1
    cout << b << endl;
}
~~~



## 三、组合

先说简单一点的组合吧，慢慢来

### 1.指针与引用

~~~c++
int *&pr1;	//这里要把它当成引用，不要被他的写法混淆了，
			//若是写成这样估计就容易懂了：int* &rp1;
			//别忘了int*也是一种类型，这个变量可以理解为引用int*的变量
			//所以他的规则跟引用一样，只是类型变成了int*
int *&pr2 = a;	//错误，只能赋值指针
int *&pr3 = p3;	//正确，此处p3为指针

int &*rp;		//呵呵，没有这种写法

//输出
cout << pr3 << endl;	//输出地址
cout << *&pr3 << endl;	//输出地址
cout << *pr3 << endl;	//改变量本质上是地址，使用解引用就能得到地址的值。输出1
~~~

同理double *&a和其他的，都是一个意思
加入指针第二阶段

~~~c++
int **&ppr = pp;	//到这里应该懂了吧，ppr就是对int **的引用，自然要赋值int**
~~~

 

接下来就是重头戏，傻傻分不清楚的const限定

### 2.const加入

简单来说，有以下形式：

~~~c++
int const * p1;
const int *	p2;
int * const p3;
const int const * p4;
~~~

第一种、第二种和第四种简单来说就是只可以修改指向，不可以修改值

![](C:\Users\czy\Desktop\Snipaste_2020-11-09_23-12-58.png)

第三种则是第一二四种反过来，不可以修改指向，但可以修改值

![](C:\Users\czy\Desktop\Snipaste_2020-11-09_23-15-47.png)

其实第一二四种都是一样的。一般来说第一种和第三种更常用。

简单记就是const离得近int近的，不可以修改值；离得远，就是不可以改指向

所以之后就拿第一种和第三种举例

**const** 加 **指针** 加 **引用** 就不写了，一样的道理

接下来看函数中const使用。

函数中const加入形参就是说，这个形参，我不希望它在方法里面被修改

先拿引用举例

~~~c++
void function(int const &i) {

	i = 3;	//此时这里会报错，因为const限定了i不可以被更改值
}
~~~

同理指针

~~~c++
void function1(int const *i) {

    int j = 2;
	*i = 3;		//此时这里会报错，因为const限定了i不可以被更改值
    i = &j;		//正确
}

void function2(int * const i) {
    int j = 2;    
    *i = 3;     //正确
    i = &j;     //此时这里会报错，因为const限定了i不可以被更改地址 
}
~~~



end