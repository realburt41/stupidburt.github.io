---
layout:     post
title:      "Unity-ShaderNote"
subtitle:   "零散的笔记"
date:       2021-4-11
author:     "Burt"
header-style: text 
tags:
    - Math
    - Shader
---



## 一、数学
- 点：有位置，没有实际大小或方向，任何一个 点 都可以看作是从 原点出发的向量
- 向量：无位置，有实际大小和方向



### 1.向量

- 点积：$\vec{a} \cdot \vec{b} = |a||b|\cos{\theta}$

  ![](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fff94ee63-7fed-4d65-9cd8-cde6c8de96eb%2FUntitled.png?table=block&id=309709b4-53b1-418b-bfd9-fb4aa0d87739&cache=v2)

  几何意义：点积描述了两个向量的 “ 相似 ” 程度 ， 点积结果越大，夹角角度越小，两个向量越接近

- 叉积：$|\vec{a} \times \vec{b}| = |a||b|\sin{\theta}$

  几何意义：

  - 两个向量的叉积可以得到同时垂直于这两个向量的向量

  - 判断左右：$\vec{a} \times \vec{b}$ 的结果为正，则 $\vec{b}$ 在 $\vec{a}$  的左侧

  - 判断一个点在一个多边形的内外：

    ![](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fcd0d00b7-5ddc-48f9-9564-183030e22801%2FUntitled.png?table=block&id=4ade4bbb-82ab-4d7b-8c52-62443794d35e&cache=v2)



### 2.矩阵

**线性方程（一次方程）**，具有：

1. 可加性（加）：$f(x_1 + x_2) = f(x_1)+ f(x_2)$
2. 比例性（乘）：$f(kx) = kf(x)$

线性方程为一元一次方程，且不涉及三角函数，同时不能出现 XY相乘

非线性方程：$x^2 + y,sinx,x+xy$



- **线性空间**：直线变换后依旧是直线，且等比（等距）坐标 原点不变 。
- **非线性空间**：空间扭曲，不等距，坐标原点有位移



矩阵最初用于解**线性方程**组



正交矩阵：一个方阵和他的转置矩阵的乘积是单位矩阵$MM^T = I$



**常见的平移矩阵操作：**

![](https://www.notion.so/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fef48681e-df3d-49db-bd44-312689ffea47%2Fimage.png?table=block&id=6ce22b42-8bbf-4494-841a-0a497e15a3c9&cache=v2)



位移矩阵不是线性变换，而是仿射变换

线性变换要求原点不能变换 ，平移会导致原点发生变换

为了让**平移矩阵**能和**其他的线性变换矩阵**一起运算 ，将平移矩阵升一个维度 ，将偏移量放入新维度中 ，新维度的分量，当为**顶点**时是1 ，为**向量**时为0



**逆矩阵：**$A^{-1} = \frac{A^*}{|A|}$

转置矩阵的逆矩阵是逆矩阵的转置

单位矩阵的逆矩阵是他本身
