---
layout:     post
title:      "3D游戏数学之其一"
subtitle:   "向量、叉积、点积....."
date:       2020-2-16
author:     "Burt"
header-img: "img/in-post/Math/Math-bg.jpg"
mathjax: true
tags:
    - Math
---



## 前言

> 数学发明创造的动力不是推理，而是想象力的发挥	——德摩根



初中的时候学向量，没想到大学的时候还要学向量。今天就来深入向量，看一看到底有什么奥秘，让我再次遇见你~~土味情话~~



---



## 一、学习向量的目的

游戏的背后的基于大量的数学运算实现的

简单的就数据加减

复杂的就各种不同的数学单位混在一起运算

不论你是学开发或者是学Shader都要用到数学

幸运的是在初级阶段涉及的数学不多（高阶的我不好说！）

但也不会太简单！

所以学习好数学，迈向大神之路吧！



向量在游戏中，代表位置，位移，有时也要和四元数进行运算

但在这个阶段我们学好向量的基本运算即可！（虽然基本，但对初学者也不算太友好）



**PS**：准备好草稿本！



## 二、初识向量

>在数学中，向量（也称为欧几里得向量、几何向量、矢量），指具有**大小**和**方向**的量

向量与标量（就是1、2、3...）的区别：

1. 向量比标量多了方向的度量
2. 标量可以被称为一维向量（即只有大小的度量）



#### 1、属性：

1. 向量的模：模代表向量的长度，模只有正数没有负数

   公式：$V_{abs} = \sqrt{(v_1)^2+(v_2)^2+...+(v_n)^2}$

   
   
   例：A（3，4，5），则 $A_{abs} = \sqrt{3^2+4^2+5^2} = 5\sqrt{2}$

#### 2、特殊向量：

1. 零向量（0，0，0）：它是唯一没有方向的向量，但他不是一个点，只是没有位移

2. 单位向量：也叫标准化向量。即只关心方向不关心大小，模为1的向量。

   公式：$V_{nor} = V \div V_{abs}$

   例：$A_{nor}(12,-5) = \frac{(12,-5)} {\sqrt{12^2+(-5)^2}} = (0.923，-0.385)$
   

**PS**：



1. 向量在图中的位置无关紧要，只有大小和方向才是最重要的
2. 数学中专门研究向量的分支叫**线性代数**
3. 个人认为：2/3维向量既表示**所在位置**，又表示**方向**以及**位移大小**





## 三、向量运算



#### 1、加减

向量加减没什么好说的，唯一值得注意的是它的几何意义

A（a1，a2，a3），B（b1，b2，b3）

A ± B = （（a1 ± b1)，（a2 ± b2），（a3 ± b3） ）



几何意义：

a+b：（口诀：首尾相连）**被加数**的头与**加数**的尾巴相连

![](/img/in-post/Math/a+b.png)

a-b：（口诀：尾尾相连）**被减数**的尾巴和**减数**的尾巴相连，结果指向**被减数**

![](/img/in-post/Math/a-b.jpg)

有了这层性质，如果我想在游戏里表达：B指向A

就可以用B-A来表示，因为结果会指向A，即指向减数





#### 2、乘法

首先我们来说简单的

##### 1）标量与向量

k = 4，A（1，2，3）

kA = （4(1)，4(2)，4(3)） = （4，8，12）

---



然后就是重头戏！向量的**点乘/积**和**叉乘/积**

为了区分点乘和叉乘的符号，我们先约定点乘符号 **·** ，叉乘符号 **×**



##### 2）点乘/积（Dot）



A（a<sub>1</sub>，a<sub>2</sub>，a<sub>3</sub>），B（b<sub>1</sub>，b<sub>2</sub>，b<sub>3</sub>）

$A \cdot B = a_{1} \cdot b_{1} + a_{2} \cdot b_{2} + a_{3} \cdot b_{3}	$

或者

$A \cdot B = A_{abs} \cdot B_{abs} \cdot \cos\theta	$

可以看出他的结果是**标量**



几何解释：

​	点乘结果描述的是两个向量的相似程度，即点乘结果越大，则两向量越相近

| A **·** B |                   θ                    |     A与B     |
| :-------: | :------------------------------------: | :----------: |
|    >0     |  0<sup>o </sup><= θ < 90<sup>o </sup>  | 方向基本相同 |
|     0     |          0 <= 90<sup>o </sup>          |     垂直     |
|    <0     | 90<sup>o </sup>< θ <= 108<sup>o </sup> | 方向基本相反 |



所以我们可以用向量来表一个物体在我们的什么方向

~~~c#
//在自身的前后，返回值为正则在前方，反之后方
Vector3.Dot(Transform.forward,target.position-tranform.position)
    
//在自身左右，返回值为正则在右方，反之左方
Vector3.Dot(Transform.right,target.position-tranform.position)
    
//在自身上下，返回值为正则在上方，反之下方
Vector3.Dot(Transform.up,target.position-tranform.position)
~~~





**PS**

- $\theta = \arccos \frac{A \cdot B}{A_{abs} \cdot B_{abs}}$
- $A \cdot B = A_{abs} \cdot B_{abs} \cdot \cos\theta	$
- 向量大小并不影响点乘结果的符号。
- 零向量与任何向量的点乘都是0，所以可以看作零向量与任何向量都垂直



---





##### 3）叉乘/积（Cross）

这个运算只可以用于3D向量

A（a<sub>1</sub>，a<sub>2</sub>，a<sub>3</sub>），B（b<sub>1</sub>，b<sub>2</sub>，b<sub>3</sub>）

A **×** B =  （a<sub>2</sub> **·** b<sub>3 </sub> - a<sub>3</sub> **·** b<sub>2 </sub>，a<sub>3</sub> **·** b<sub>1</sub> - a<sub>1</sub> **·** b<sub>3</sub> ，a<sub>1</sub> **·** b<sub>2</sub> - a<sub>2</sub> **·** b<sub>1</sub> ）

叉积得到另一个向量，不满足交换律，但是满足反交换律，即 A **×** B = -（A **×** B）



几何解释：

​	叉积得到的向量垂直于原来两个向量



叉积最重要的应用就是创建垂直于平面、三角形或多边形的向量，即法线。



**PS**：

- 如果叉积和点积同在一条算式中，叉积优先级较高
- $(A \times B)_{abs} = A_{abs} \times B_{abs} \times \sin\theta$
- 零向量与叉积平行





---



## 四、扩展



#### 1、向量投影

一般而言，投影是降维操作

给定向量A和B

![](/img/in-post/Math/Projection.jpg)

B投影到A上得到B<sub>1</sub>

此时有三条线段OB，OB<sub>1</sub>和B<sub>1</sub>B

其中

- B<sub>1</sub>B与OA垂直
- OB<sub>1</sub>与OA平行
- OB = OB<sub>1</sub> + B<sub>1</sub>B

此时称OB<sub>1</sub>为向量B在向量A上的投影

一般来说，求OB<sub>1</sub>，即向量B在向量A的投影为：OB<sub>1</sub> =A × OB<sub>1</sub><sub>abs</sub> / A<sub>abs</sub>

这样很绕，但幸运的是，使用三角分解可以更方便的算出结果

cosθ = OB<sub>1</sub><sub>abs</sub> / B<sub>abs</sub>

等价于

OB<sub>1</sub><sub>abs</sub> =  cosθ × B<sub>abs</sub>

所以

OB<sub>1</sub> = A × cosθ × B<sub>abs </sub>/ A<sub>abs</sub>

​		= A ×  A<sub>abs </sub>× B<sub>abs</sub> × cosθ / A<sub>abs</sub><sup>2</sup>

​		= A  ×  A × B /  A<sub>abs</sub> <sup>2</sup>

假如A是单位向量，那么也就不用除法了

直接为

OB<sub>1</sub> =  A  ×  A × B 

这样可以省去很多运算

此时就有公式：

- B<sub>1</sub>B = B<sub>abs</sub> - OB<sub>1</sub> =  B<sub>abs</sub> - A<sup>2</sup> × B



#### 2、线性代数公式



1. A **·**（B + C） = A **·** B + A **·** C
2. A **×**（B + C） = A **×** B + A **×** C
3. A **×** A = 0（任何向量与自身叉积等于0）
4. A **×** B = -（B **×** A） = -B × -A
5. k（A **×** B）= kA × B = A × kB
6. A **·**（A **×** B）= 0（向量与另一向量的叉积在点积该向量本身等于0）





待续。。。





