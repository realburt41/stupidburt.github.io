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



Reference：https://zhuanlan.zhihu.com/p/36302775