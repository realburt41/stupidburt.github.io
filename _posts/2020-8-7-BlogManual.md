---
layout:     post
title:      "博客使用手册"
subtitle:   "这个是怎么弄的..."
date:       2020-8-7
author:     "Burt"
header-img: "img/in-post/Blog/BlogManual-bg.jpg"
header-mask: 1
tags:
    - Blog
---





## 一、前言

整理一下该博客的使用方式





## 二、图片相关

##### 1、为图片增加一个黑色的mask

~~~js
header-mask: 0.3
~~~

##### 2、Latex支持

~~~js
mathjax: true
~~~

##### 3、不显示head picture

~~~js
header-style: text 
~~~

##### 4、图片路径

~~~js
"/img/in-post/..."
~~~





## 三、Unity WebGL

##### 1、JS呼叫Unity

JS：

~~~javascript
function JS方法(){
	untiyInstance.SendMessage("场景中的物体","方法名","参数");
}
~~~

Unity：

~~~c#
 Application.ExternalCall("JS方法");
~~~

但是在Unity新版本不推荐这样做，而是使用这个<a href = "https://docs.unity3d.com/Manual/webgl-interactingwithbrowserscripting.html">方法</a>。

示例代码：

~~~html
<html lang="en-us">

  <head>
    <link rel="stylesheet" href="/assets/Unity/Shader/TemplateData/style.css">
    <script src="/assets/Unity/Shader/TemplateData/UnityProgress.js"></script>  
    <script src="/assets/Unity/Shader/Build/UnityLoader.js"></script>
    <script>
      var unityInstance = UnityLoader.instantiate("gameContainer", "/assets/Unity/Shader/Build/Shader.json", {onProgress: UnityProgress});
        function changeSceneName(){
         unityInstance.SendMessage("SceneManager","setSceneName","AlphaBlend");
      }
    </script>
  </head>
  <body>
    <div class="webgl-content">
      <div id="gameContainer" style="width: 700px; height: 470px"></div>
      <div class="footer">
        <div class="webgl-logo"></div>
        <div class="fullscreen" onclick="gameInstance.SetFullscreen(1)"></div>
      </div>
    </div>
  </body>
</html>
~~~





## 四、博客文案

##### 1、格式约定

- 标题#
  - 大标题 ##
  - 小标题 #####
- 标题间空格
  - 大标题与大标题之间空两格
  - 小标题与小标题之间无需空格
- 图片
  - 非自创图片说明文案：标明出处
  - 自创图片说明文案：自定义





待续...