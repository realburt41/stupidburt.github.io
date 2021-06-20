---
layout:     post
title:      "Github使用手册"
subtitle:   ""
date:       2020-8-7
author:     "Burt"
header-img: "img/in-post/Github/Github-bg.jpg"
header-mask: 0.5
tags:
    - Github

---



## 说明

整理一下Github的使用方式



## git指令



### 一、创建

~~~c
git init	//创建本地仓库
git status	//查看状态
git log		//查看日志
~~~

- 仓库区：git文件夹为仓库区，类似一个数据库存储着每一次提交的变化
- 工作区：git所在目录称为工作区，我们在这里创建项目和其他文件







### 一、更新

**1.简易更新**

~~~c
1、git add .     			//添加所有文件
2、git pull github master  	//拉来名为github分支
3、git commit -m "说明文字"	 //说明
4、git push github master   //上传
~~~

git add <文件名>可以把文件添加到**暂存区**

暂存区存储将要被提交的文件变化



commit命令是指提交暂存区存储的变化并生成一个新的版本





## Tortoise git



### 一、推送失败

网络的SSH修改为使用git默认的ssh客户端，而不是tortosieGit提供的客户端

![](https://images2017.cnblogs.com/blog/679411/201802/679411-20180204093027170-688143560.png)

本机凭证修改为当前用户

![](https://images2017.cnblogs.com/blog/679411/201802/679411-20180204093033810-433111841.png)