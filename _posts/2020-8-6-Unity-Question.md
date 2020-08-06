---
layout:     post
title:      "收集的Unity面试题集"
subtitle:   "今天你学习了吗"
date:       2020-8-6
author:     "Burt"
header-img: "img/in-post/Camera/Camera-bg.jpg"
tags:
    - Unity
---







在此记录面试题，不管是会的还是不会的，都加强巩固，自强不息

References:

https://zhuanlan.zhihu.com/p/161736640



#### c#

##### 1、拆箱与装箱

装箱：值类型隐式转换为object类型 或 由此值类型实现的任何接口类型 的过程

1. 在堆中开辟内存空间
2. 将值类型的数据复制到堆中
3. 返回堆中新分配对象的地址

拆箱：上述逆过程

1. 判断给定类型是否是装箱时的类型
2. 返回已装箱实例中属于原值类型字段的地址



##### 2、string str = null与string str = "" 区别

str = null表示空引用，不占空间

str = ""   表示初始化对象并为其分配空间



##### 3、ref和out 区别

...



##### 4、序列化与反序列化是什么

序列化是将对象状态转换为可保持或传输的格式的过程

反序列化则是将流转换为对象。

这两个过程结合起来，可以轻松实现存储和传输数据。



##### 5、sealed修饰符

...



待续...
