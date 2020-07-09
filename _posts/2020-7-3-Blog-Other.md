---
layout:     post
title:      "博客搭建"
subtitle:   "遇到的一些问题"
date:       2020-7-3
author:     "Burt"
header-img: "img/in-post/Camera/Camera-bg.jpg"
tags:
    - Blog
---

<head>
    <link rel="shortcut icon" href="TemplateData/favicon.ico">
    <link rel="stylesheet" href="TemplateData/style.css">
    <script src="TemplateData/UnityProgress.js"></script>
    <script src="Build/UnityLoader.js"></script>
    <script>
      var unityInstance = UnityLoader.instantiate("unityContainer", "Build/_unity.json", {onProgress: UnityProgress});
      function changeSceneName(){
         unityInstance.SendMessage("SceneManager","setSceneName","RimLight_Lambert");
      }
    </script>
</head>


#### 让GitHub Page支持Latex公式

笔者用的是Typora，

- 在Typora设置：文件→偏好设置→Markdown→Markdown语法，全部勾选

- 一般Typora的_config.yml 文件设置markdown引擎为kramdown，如果不是那就设置为kramdown

- 在页面的html里的<head>标签加入

  ```html
   <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
      <script type="text/x-mathjax-config">
          MathJax.Hub.Config({
              tex2jax: {
              skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
              inlineMath: [['$','$']]
              }
          });
      </script>
  ```



#### 更新

博客作者已经贴心的帮我们实现了这一功能，只需要在 post 顶上加 `mathjax: true`即可



#### 测试

xxxxx完了

下面一起来看一下效果吧：


<div class="webgl-content">
      <div id="unityContainer" style="width: 960px; height: 600px"></div>
      <div class="footer">
        <div class="webgl-logo"></div>
        <div class="fullscreen" onclick="unityInstance.SetFullscreen(1)"></div>
        <div class="title">RimLight</div>
      </div>
</div>






Reference：https://zhuanlan.zhihu.com/p/36302775
