---
layout:     post
title:      "Shader知识点"
subtitle:   "滴滴"
date:       2020-7-20
author:     "Burt"
header-style: text 
tags:
    - Unity
    - Shader
---





## 前言

整理零散的知识点



## 正文



### 一、语义

**1.SV_POSITION与POSITION**

- SV 是 System Value的意思
- POSITION 顶点的坐标
- SV_POSITION 是默认固定的系统值，不得改变



**2.SV_Target与Color**

- COLOR 顶点颜色
- SV_TARGET 是系统值，表示该函数返回的是用于下一个阶段OutPut Merger的颜色值，这里只简单的做了颜色值得传递。



### 二、渲染路径



**1.前向渲染(Forward Render)**

用于**Unity内置渲染管线Build-in**与**Unity URP渲染管线**

![](https://cdn.tutsplus.com/gamedev/uploads/2013/11/forward-v2.png)

先执行着色计算，再执行深度测试

前向渲染渲染 n 个物体在 m 个光源下的着色，复杂度为 O(n*m)次

光源数量对计算复杂度影响巨大，所以比较适合户外这种光源较少的场景

伪代码：

~~~python
For each light:
    For each object affected by the light:
        framebuffer += object *light
~~~



在Shader中主要由两个Pass进行计算

- Forward Base：在这个Pass里会传入**主方向灯**和**超出引擎规定数量范围外而变成顶点灯的灯**（基本不用，可以忽略）；SH、LightMap、Reflection Probe等计算均在这个Pass里面完成
- Forward Add：传入**引擎规定数量范围外的灯（点光、平行光等）**。每个灯光的计算，均会调用一次该Pass进行计算。计算的结果会用Blend One One叠加起来，如果不开启会替换掉Base的渲染





**2.延迟渲染(Deferred Render)**

用于**UE4默认渲染管线**与**Unity HDRP渲染管线**

![](https://cdn.tutsplus.com/gamedev/uploads/2013/11/deferred-v2.png)

先执行深度测试，再执行着色计算

延迟渲染渲染 n 个物体在 m 个光源下的着色，复杂度为 O(n+m)次

最大的优势就是将光源的数目和场景中物体的数目在复杂度层面上完全分开。 也就是说场景中不管是一个三角形还是一百万个三角形，最后的复杂度不会随 光源数目变化而产生巨大变化

伪代码：

~~~python
For each object:
    Render to multiple targets
For each light:
    Apply light as a 2D postprocess
~~~



在Shader中主要由两个Pass进行计算

- 第一个Pass：不进行任何光照计算，而是计算哪些片元可见。这主要使用深度缓冲技术实现，当发现一个片元是可见的，我们就把它的相关信息存储到G缓冲区中
- 第二个Pass：利用G缓冲区的每个片元信息，例如表面法线、视角方向、漫反射系数等，进行真正的光照计算



前向渲染vs延迟渲染

- 复杂度：前向渲染O(n*m)，延迟渲染仅O(n+m)
- 光源：前向渲染光源越多消耗越大，延迟渲染对于这种场景有优势
- 内存带宽：由于延迟渲染需要G-Buffer来保存信息所以对内存和带宽需求更大，读写G-buffer的内存带宽用量是性能瓶颈
- 透明：延迟渲染对透明物体的渲染存在问题。在这点上需要结合正向渲染进行渲染
- 抗锯齿：延迟渲染对多重采样抗锯齿（MultiSampling Anti-Aliasing, MSAA）的支持不友好，主要因为需开启MRT



参考：https://blog.csdn.net/poem_qianmo/article/details/77142101



### 三、Early-Z

转自：https://blog.csdn.net/puppet_master/article/details/53900568



传统的渲染管线中，ZTest其实是在Blending阶段，这时候进行深度测试，所有对象的像素着色器都会计算一遍，没有什么性能提升，仅仅是为了得出正确的遮挡结果，会造成大量的无用计算，因为每个像素点上肯定重叠了很多计算。因此 现代GPU中运用了Early-Z的技术，在Vertex阶段和Fragment阶段之间（光栅化之后，fragment之前）进行一次深度测试，如果深度测试失败，就不必进行fragment阶段的计算了，因此在性能上会有很大的提升。但是最终的ZTest仍然需要进行，以保证最终的遮挡关系结果正确。前面的一次主要是Z-Cull为了裁剪以达到优化的目的，后一次主要是Z-Check，为了检查，如下图： 

![](https://img-blog.csdn.net/20170101225548583?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVwcGV0X21hc3Rlcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

Early-Z的实现，主要是通过一个Z-pre-pass实现，简单来说，对于所有不透明的物体（透明的没有用，本身不会写深度），首先用一个超级简单的shader进行渲染，这个shader不写颜色缓冲区，只写深度缓冲区，第二个pass关闭深度写入，开启深度测试，用正常的shader进行渲染。其实这种技术，我们也可以借鉴，在渲染透明物体时，因为关闭了深度写入，有时候会有其他不透明的部分遮挡住透明的部分，而我们其实不希望他们被遮挡，仅仅希望被遮挡的物体半透，这时我们就可以用两个pass来渲染，第一个pass使用Color Mask屏蔽颜色写入，仅写入深度，第二个pass正常渲染半透，关闭深度写入


