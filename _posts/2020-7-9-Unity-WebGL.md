---
layout:     post
title:      "Unity WebGL"
subtitle:   "遇到的一些问题"
date:       2020-7-9
author:     "Burt"
header-img: "img/in-post/Camera/Camera-bg.jpg"
tags:
    - Unity
    - WebGL
---





#### 妈蛋

花了一个早上时间找unity与js互相传值的方法，api搞得真难用，气死人



#### JS 调用 Unity

一开始我天真的以为，JS调用Unity一句话就可以搞定

~~~
//JS里
untiyInstance.SendMessage("场景中的物体","方法名","参数");
~~~

没想到Unity是要我来调用你的调用，晕了

更正：

~~~
 //unity里
 Application.ExternalCall("JS方法");
~~~

然后JS里要实现这个方法

~~~
//JS里
function JS方法(){
	untiyInstance.SendMessage("场景中的物体","方法名","参数");
}
~~~

到这里其实就搞定了

但是如果你直接用Application.ExternalCall这个函数，IDE会告诉你这个方法过时了，还在方法下面加个波浪线，还给出一个<a href = "https://docs.unity3d.com/Manual/webgl-interactingwithbrowserscripting.html">链接</a>让你去看，作为强迫症当然不能忍，go！

简单描述一下链接里的内容：

首先需要写一个JS的脚本，主要是调用mergeInto();方法，第一个参数不用变，第二个参数就是JS的方法集合。写完之后将这个文件的后缀改为.jslib，放到Unity里的Plugins文件夹中

~~~
mergeInto(LibraryManager.library, 
{
 
  Hello: function ()
  {
    window.alert("Hello, world!");
  },
 
  HelloString: function (str) 
  {
    window.alert(Pointer_stringify(str));
  },
 
 
 });
~~~

之后打包就去build找这个文件，后期更改什么的

Unity里调用则是

~~~
using UnityEngine;
using System.Runtime.InteropServices;
 
public class TestJS : MonoBehaviour
{
	//方法1
    [DllImport("__Internal")]
    private static extern void Hello();
 
 	//方法2
    [DllImport("__Internal")]
    private static extern void HelloString(string str);

    void Start()
    {
        Hello();
        HelloString("This is a string.");
    }


~~~

相应的打包出来的index.html就不用再去改了。

真的是好麻烦，但是官方说这样会更安全，那就这样吧

