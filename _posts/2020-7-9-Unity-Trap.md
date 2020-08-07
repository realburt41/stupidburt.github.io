---
layout:     post
title:      "Unity开发中的坑"
subtitle:   "遇到的一些问题"
date:       2020-7-10
author:     "Burt"
header-img: "img/in-post/Unity/Trap/Trap-bg.jpg"
header-mask: 0.3
tags:
    - Unity
    - Trap
---





## 妈蛋

好多坑啊，大家小心点



### 1.旋转角度

Unity面板用欧拉角来显示，然后运算用四元数，四元数本身有点复杂，而欧拉角比较直观，那么我现在问你：

Quaternion.Euler(0 , -1 , 0)是多少度

。。。

面板上Rotation也确实是(0 , -1 , 0)

但是运行时就不一样了，你打印出来，它会显示(0,  359 , 0)

是不是很坑，那么我们就自己在做一层，强行变成负数

~~~c#
void Start()
{
        transform.rotation = Quaternion.Euler(0, -1, 0);
        print(transform.rotation.eulerAngles.y);
        print(CheckAngle(transform.rotation.eulerAngles.y));
}


public float CheckAngle(float value)
{
        float angle = value - 180;

        if (angle > 0)
            return angle - 180;

        return angle + 180;
}
~~~

运行结果都是-1



Reference: https://blog.csdn.net/qq_15386973/article/details/76255694



### 2.Awake与Start



#### 2-1

有这样一个脚本：

~~~
using UnityEngine;
using System.Collections;
 
public class Component : MonoBehaviour {
 
    void Awake()
    {
        Debug.Log("a");
    }
 
    void Start()
    {
        Debug.Log("b");
    }

}
~~~

当我这个脚本没被激活，谁会被执行

3

2

1

你以为两个都不会执行？

其实会执行Awake啦！Start不会被执行

此时你再激活脚本，Start才执行

但是**注意**，我上面说的是脚本没被激活，但是当挂着脚本的物体没被激活，那么Awake和Start都不会执行



#### 2-2

首先我们先有一个脚本：

~~~c#
using UnityEngine;
using System.Collections;
 
public class Component2 : MonoBehaviour {
 
    public void Test()
    {
        Debug.Log("c");
    }
}
~~~

再来一个脚本：

~~~c#
using UnityEngine;
using System.Collections;
 
public class Component1 : MonoBehaviour {
 
	
    void Awake()
    {
        Debug.Log("a");
    }
 
    void Start()
    {
        Debug.Log("b");
        GameObject go = new GameObject("new Obj");
        go.AddComponent<Component2>();
        go.GetComponent<Component2>().Test();
    }
 
}
~~~

给你三秒思考执行顺序是什么

3

2

1

你以为答案是abc？

其实是acb哒！

这里应该是很容易出现问题的地方，比如Test函数中要用到一些值，而这些值应该被初始化，如果我们把初始化放在了Start函数中，那么此处这些值还没有被初始化，那么就会出现空引用异常等错误。





待续...