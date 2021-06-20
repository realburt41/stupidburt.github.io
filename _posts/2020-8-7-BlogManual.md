---
layout:     post
title:      "博客使用手册"
subtitle:   ""
date:       2020-8-7
author:     "Burt"
header-img: "img/in-post/Blog/BlogManual-bg.jpg"
onTop: true
header-mask: 0.5
tags:
    - Other
---



## 说明

整理一下博客的使用方式



## 内容



### 一、功能



#### 1.为图片增加一个黑色的mask

~~~c
header-mask: 0.3
~~~



#### 2.mathjax支持

~~~c
mathjax: true
~~~



#### 3.不显示head picture

~~~c
header-style: text 
~~~



#### 4.图片路径

~~~c
"/img/in-post/..."
~~~



#### 5.文章置顶

~~~c
onTop: true
~~~

[文章置顶实现](https://github.com/Huxpro/huxpro.github.io/issues/353)



### 二、Unity WebGL



#### 1.JS呼叫Unity

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

但是在Unity新版本不推荐这样做，而是使用这个[方法](https://docs.unity3d.com/Manual/webgl-interactingwithbrowserscripting.html)。

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



### 三、评论

gitalk，假如无法使用，记得查看OAuth Apps中的网站地址配置是否正确



### 四、jekyll

ruby版本不要太高，不然不兼容jekyll导致无法本地部署

![](https://img-blog.csdnimg.cn/20210609094318673.png)



之后到达目标路径，正常jekyll serve即可