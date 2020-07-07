---
layout:     post
title:      "游戏摄像机控制之其一"
subtitle:   "第三人称相机基本功能"
date:       2020-2-15
author:     "Burt"
header-img: "img/in-post/Camera/Camera-bg.jpg"
catalog: true
tags:
    - Unity
    - Camera
---



## 前言

> Hello World!


---




## 目的以及成果 



今天我们要来实现动作游戏内的最基本的——摄！相！机！移！动！

![CameraRevolve](/img/in-post/Camera/revolve.gif)

<small class="img-hint">目标是盯着这个Cube！</small>



## 思路

首先在Main Camera建立一个脚本。

其次我们要知道，相机移动是通过鼠标移动完成

所以首先我们需要获取鼠标输入

```c#
float x = Input.GetAxis("Mouse X");
float y = Input.GetAxis("Mouse Y");
```

但是，Input.GetAxis("Mouse X/Mouse Y")传进来的值是鼠标x，y坐标的增减量，所以我们的代码要改成

```c#
float x += -Input.GetAxis("Mouse X") * isReverseX == true ? -1 : 1;	
float y += Input.GetAxis("Mouse Y") * isReverseY == true ? -1 : 1;
//isReverse是否要反转轴
```

之后我们的x，y就会变成相机移动的基准二维坐标值

转换为四元数，我们的相机就要根据下面的tempRotation来自旋转

```c#
Quaternion tempRotation = Quaternion.Euler(y, x, 0);
```

定义相机与目标的距离
```c#
Vector3 dis = new Vector3(0.0f, 0.0f, -distance);
```

接下来就是关键一步

```c#
Vector3 tempPosition = tempRotation * dis + target.position	//target.position是角色偏移量
```

Quaternion与Vector3的乘积是啥意思？

首先我们要知道Quaternion.Euler(y,x,0)表示

返回一个绕x轴旋转y度，再绕y轴旋转x度的Quaternion

例如Quaternion.Euler(0,90, 0);

就是返回一个绕y轴旋转90度的Quaternion

Quaternion与Vector3的乘积相当于Quaternion的旋转作用在Vector身上

所以当我们看到Quaternion.Euler(0,90, 0)与Vector3(0,0,10)的乘积

会返回一个Vector3(0,0,10)

具体为什么会这样请看。。。。

所以说了这么多，这句代码的意思就是让dis变量按照tempRotation的值旋转位置

之后再把位置赋值给相机位置就可以啦！
```c#
transform.rotation = rotation;
transform.position = position;
```





## 完整代码

```c#
using System.Collections.Generic;
using UnityEngine;

public class CameraFollow : MonoBehaviour {

    public Transform target;		//跟随的目标
    
    public float distance = 5.0f;	//相机距离目标几米
    public float xSpeed = 60.0f;	//x旋转速度
    public float ySpeed = 60.0f;	//y旋转速度

    public float height = 1.3f;		//跟随的高度

    public float yMinLimit = -60f;	//限制y轴的范围
    public float yMaxLimit = 60f;
    
    public bool isReverseX = false;
    public bool isReverseY = false;
    
    public float x = 0.0f;
    public float y = 0.0f;

    // Use this for initialization
    void Start()
    {
        Vector3 angles = transform.eulerAngles;	//获取相机初始角度，从当前角度开始旋转
        x = angles.y;
        y = angles.x;
    }

    void LateUpdate()
    {
        if (target)
        {
           	x += Input.GetAxis("Mouse X") * xSpeed * isReverseX == true ? -1 : 1;
            y += Input.GetAxis("Mouse Y") * ySpeed * isReverseY == true ? -1 : 1;
			
            //使y始终保持在取值范围内
            y = Mathf.Clamp(angle, min, max);	

            Quaternion tempRotation = Quaternion.Euler(y, x, 0);

            Vector3 dis = new Vector3(0.0f, 0.0f, -distance);
            Vector3 tempPosition = tempRotation * dis + target.position + new Vector3(0,height,0);

            transform.rotation = tempRotation;
            transform.position = tempPosition;
        }
    }

}
```





## 总结

1. 先获取鼠标输入
2. 将鼠标输入传进一个四元数tempRotation来保存鼠标位移，相机自身将根据tempRotation来旋转，类似地球自转
3. 建立局部向量dis和tempPosition，前者记录相机与玩家位置的距离，后者根据dis和tempRotation确定鼠标移动后的相机位置，类似地球绕太阳公转
4. 赋值



PS：

- 代码放在LateUpdate，是因为相机是在玩家移动之后再移动，玩家的行为一般放在Update函数，LateUpdate函数则是再Update函数之后进行。

  
