---
layout:     post
title:      "角色控制（一）"
subtitle:   "让你的小人动起来"
date:       2020-2-17
author:     "Burt"
header-img:  "img/in-post/Character/Character-bg.jpg"
tags:
    - Unity
    - Charater
---



## 前言

> 动作游戏，最基本的就是角色的移动啦~

---

## Catalog


1. [目的以及成果](#目的以及成果)
4. [思路](#思路)
3. [完整代码](#完整代码)
4. [总结](#总结)





## 目的以及成果

角色滴自由移动~~，包括跳跃，翻滚，攻击！~~

![CharacterMove](/img/in-post/Character/Character-move1.gif)

<small class="img-hint">在这片自由的世界迈出第一步</small>





## 思路

这里我假设你已经对**Unity的动画系统**有了了解

如果还不了解👉[链接](#)

分割线-----------------------------------------------------------

在角色的Animator，我们新建一个**blend tree**

只需要1D方向，0到1，0为idle，1为Run

建立一个变量叫`InputMagnitude`

![Animator](/img/in-post/Character/blendtree.png)

记得把角色Animator Controller给到角色的Animator

接下来开始代码思路：

角色行动是基于玩家输入

首先获取到玩家的输入，然后对玩家的输入进行处理

```c#
vInput = Input.GetAxis("Vertical");			//获取纵轴输入，代表前后，即w、s、↑、↓键
hInput = Input.GetAxis("Horizontal");		//获取横轴输入，代表左右，即a、d、←、→键

//对玩家的输入进行处理
inputMagnitude = Mathf.Sqrt(vInput * vInput + hInput * hInput);		
```

代码第五行的意思是：~~勾股定理~~

只要玩家有输入，不论按几个键，都处理为约等于1

为什么约等于1

因为当你同时按下**前左/右**或**后左/右**

1+1=2的根号为1.414.....约等于1！Animator controller限制数值变量最大为1，所以即使超过1一点点也没关系

然后把脚本变量inputMagnitude的值赋给Animator controller里的inputMagnitude

```c#
anim.SetFloat("InputMagnitude", inputMagnitude);
```

这个时候Animator controller里的inputMagnitude就可以根据玩家的输入调整

**接下来就是关键啦，我们要确定角色的行走方向**

一般角色行走的方向都是参照相机方向，这样才比较好掌控角色方向

我们获取相机的Transform中的forward和right参数

分别获取相机指向前方向量和右方向量，相应的，指向后方向量 = - 指向前方向量，左方向量同理

但是我们什么时候知道角色转向前还是右

当然是根据我们玩家的输入啦

```c#
vInput * camTran.forward + hInput * camTran.right;
```

但是我们获取到的这个方向，不需要向上或者向上的方向，不然角色的身子就朝天或者朝地，所以要清空掉方向里的y值，赋值给direction

```c#
//x和z保持原样，清空y
direction = Vector3.Scale((vInput * camTran.forward + hInput * camTran.right, new Vector3(1, 0, 1));
```

解决掉了方向问题，但是我们还没告诉角色说，xxx你应该怎么转呀

Unity给我们提供了一个函数叫Quaternion.LookRotation(Vector3 forward, Vector3 upward)

这个函数指的是：返回一个转向forward的四元数，upward确定物体的正上方方向

所以我们最终的方向向量转换为四元数：

```c#
Quaternion.LookRotation(direction, Vector3.up)
```

但是我们不能直接赋值呀！不然角色的转向就像瞬移一样，没有过渡，一点也不自然

```c#
//这个函数是使第一个参数慢慢加/减至第二个参数，第三个参数表示每帧加/减第二个参数百分之多少的量
Quaternion.Lerp(this.transform.rotation, Quaternion.LookRotation(direction, Vector3.up), 0.3f);
```

最后赋值给角色的transform的rotation

还没完！

假如我们的代码就这样编译执行，会发生。。

![error](/img/in-post/Character/Character-move2.gif)

<small class="img-hint">我没有按转向前！</small>

是因为我们如果没有输入，direction就会变为0，就是始终转向Vector3(0,0,0)的方向，所以我们需要加上：

```c#
 if (vInput != 0 || hInput != 0)	//add this
            this.transform.rotation = Quaternion.Lerp(this.transform.rotation, Quaternion.LookRotation(direction, Vector3.up), 0.3f);
```





## 完整代码

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class SimpleController : MonoBehaviour {

    //Input----------------------------
    private float vInput;
    private float hInput;

    //Move-----------------------------
    private Vector3 direction;
    private float inputMagnitude;

    //相机Transform
    private Transform camTran;
	
    void Awake(){
        //获取主相机Transform
        camTran = Camera.main.transform;
    }
    
	// Update is called once per frame
	void Update () {

        //获取输入--------------------------------------
        vInput = Input.GetAxis("Vertical");
        hInput = Input.GetAxis("Horizontal");

        inputMagnitude = Mathf.Sqrt(vInput * vInput + hInput * hInput);

        anim.SetFloat("InputMagnitude", inputMagnitude);
        
        //根据输入转向--------------------------------------
        
        direction = Vector3.Scale((vInput * camTran.forward + hInput * camTran.right), new Vector3(1, 0, 1));
        if (vInput != 0 || hInput != 0)
            this.transform.rotation = Quaternion.Lerp(this.transform.rotation, Quaternion.LookRotation(direction, Vector3.up), 0.3f);

    }
```





## 总结

- 配置好动画
- 获取玩家输入
- 角色根据玩家输入转向



这就迈出了动作游戏的第一步啦！

