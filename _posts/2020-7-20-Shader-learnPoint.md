---
layout:     post
title:      "Shader知识点"
subtitle:   "这背景图片真好看"
date:       2020-7-20
author:     "Burt"
header-img: "img/in-post/Unity/Shader/ShaderLearnPoint-bg.jpg"
tags:
    - Unity
    - Shader
---

<section id="nice" data-tool="mdnice编辑器" data-website="https://www.mdnice.com" style="font-size: 16px; color: black; padding: 0 10px; line-height: 1.6; word-spacing: 0px; letter-spacing: 0px; word-break: break-word; word-wrap: break-word; text-align: left; font-family: Optima-Regular, Optima, PingFangSC-light, PingFangTC-light, 'PingFang SC', Cambria, Cochin, Georgia, Times, 'Times New Roman', serif;"><h2 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; border-bottom: 2px solid#CBCAAF; font-size: 1.3em;"><span class="prefix" style="display: none;"></span><span class="content" style="display: inline-block; font-weight: bold; background: #CBCAAF; color: #ffffff; padding: 3px 10px 1px; border-top-right-radius: 3px; border-top-left-radius: 3px; margin-right: 3px;">前言</span><span class="suffix"></span><span style="display: inline-block; vertical-align: bottom; border-bottom: 36px solid #efebe9; border-right: 20px solid transparent;"> </span></h2>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">Shader各类语义很多，用到了许多知识，但是当你实现了效果，然鹅不知道为什么会这样，那就很尴尬，有囫囵吞枣的感觉，这一次梳理一下我在编写Shader代码时遇到的语义及其知识点。</p>
<h2 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; border-bottom: 2px solid#CBCAAF; font-size: 1.3em;"><span class="prefix" style="display: none;"></span><span class="content" style="display: inline-block; font-weight: bold; background: #CBCAAF; color: #ffffff; padding: 3px 10px 1px; border-top-right-radius: 3px; border-top-left-radius: 3px; margin-right: 3px;">知识点</span><span class="suffix"></span><span style="display: inline-block; vertical-align: bottom; border-bottom: 36px solid #efebe9; border-right: 20px solid transparent;"> </span></h2>
<h3 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; font-size: 20px;"><span class="prefix" style="display: none;"></span><span class="content">一、语义</span><span class="suffix" style="display: none;"></span></h3>
<h4 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; font-size: 18px;"><span class="prefix" style="display: none;"></span><span class="content">1.SV_POSITION与POSITION</span><span class="suffix" style="display: none;"></span></h4>
<ul data-tool="mdnice编辑器" style="margin-top: 8px; margin-bottom: 8px; padding-left: 25px; color: black; list-style-type: disc;">
<li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">SV 是 System Value的意思</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">POSITION 顶点的坐标</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">SV_POSITION 是默认固定的系统值，不得改变</section></li></ul>
<h4 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; font-size: 18px;"><span class="prefix" style="display: none;"></span><span class="content">2.SV_Target与Color</span><span class="suffix" style="display: none;"></span></h4>
<ul data-tool="mdnice编辑器" style="margin-top: 8px; margin-bottom: 8px; padding-left: 25px; color: black; list-style-type: disc;">
<li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">COLOR 顶点颜色</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">SV_TARGET 是系统值，表示该函数返回的是用于下一个阶段OutPut Merger的颜色值，这里只简单的做了颜色值得传递。</section></li></ul>
<h4 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; font-size: 18px;"><span class="prefix" style="display: none;"></span><span class="content">3.ZWRITE &amp;&amp; ZTEST</span><span class="suffix" style="display: none;"></span></h4>
<ul data-tool="mdnice编辑器" style="margin-top: 8px; margin-bottom: 8px; padding-left: 25px; color: black; list-style-type: disc;">
<li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">ZWRITE表示是否将<strong style="font-weight: bold; color: black;">深度</strong>写入GBuffer中</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">ZTEST表示是否将<strong style="font-weight: bold; color: black;">颜色</strong>写入GBUFFER中</section></li></ul>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">ZTEST将要渲染物体的深度与ZWRITE中写入的深度进行比较，符合要求则渲染，不符合则不渲染</p>
<h4 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; font-size: 18px;"><span class="prefix" style="display: none;"></span><span class="content">4.Queue标签</span><span class="suffix" style="display: none;"></span></h4>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">定义渲染顺序，值较大的靠后渲染，值较小的靠前渲染。预定义的值为：</p>
<ol data-tool="mdnice编辑器" style="margin-top: 8px; margin-bottom: 8px; padding-left: 25px; color: black; list-style-type: decimal;">
<li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">Background（1000） 最早被渲染的物体的队列。</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">Geometry    （2000） 不透明物体的渲染队列。大多数物体都应该使用该队列进行渲染，也是Unity Shader中默认的渲染队列。</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">AlphaTest    （2450） 有透明通道，需要进行Alpha Test的物体的队列，比在Geomerty中更有效。</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">Transparent（3000） 半透物体的渲染队列。一般是不写深度的物体，Alpha Blend等的在该队列渲染。</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">Overlay        （4000） 最后被渲染的物体的队列，一般是覆盖效果，比如镜头光晕，屏幕贴片之类的。</section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;">用户可以定义任意值，比如”Queue”=”Geometry+10”</section></li></ol>
<h3 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; font-size: 20px;"><span class="prefix" style="display: none;"></span><span class="content">二、基本概念</span><span class="suffix" style="display: none;"></span></h3>
<h4 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; font-size: 18px;"><span class="prefix" style="display: none;"></span><span class="content">1.什么是深度？</span><span class="suffix" style="display: none;"></span></h4>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">深度其实就是该像素点在3d世界中距离摄像机的距离。离摄像机越远，则深度值（Z值）越大。</p>
<h4 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; font-size: 18px;"><span class="prefix" style="display: none;"></span><span class="content">2.什么是深度缓存？</span><span class="suffix" style="display: none;"></span></h4>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">深度缓存中存储着准备要绘制在屏幕上的像素点的深度值。如果启用了深度缓冲区，在绘制每个像素之前，OpenGL会把该像素的深度值和深度缓存的深度值进行比较。如果 新像素深度值 &lt; 深度缓存深度值，则新像素值会取代原先的；反之，新像素值被遮挡，其颜色值和深度将被丢弃。</p>
<h4 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; font-size: 18px;"><span class="prefix" style="display: none;"></span><span class="content">3.为什么法线贴图都是蓝色的</span><span class="suffix" style="display: none;"></span></h4>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">法线贴图中存储的是切线空间的法线。而法线贴图所对应的表面，绝大部分的位置肯定是平滑的，只有需要凹凸变化的地方才会有变化，那么大部分地方的法线方向不变，也就是在切线空间的（0,0,1），这个值按照上面介绍的映射关系，从（-1,1）区间变换到（0,1）区间：（0<em style="font-style: italic; color: black;">0.5+0.5,0</em>0.5+0.5,1*0.5+0.5）= （0.5,0.5,1），再转化为颜色的（0,255）区间，最终就变成了（127,127,255）这个颜色即为法线贴图的颜色。</p>
<h4 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; font-size: 18px;"><span class="prefix" style="display: none;"></span><span class="content">4.什么是PBS</span><span class="suffix" style="display: none;"></span></h4>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">从字面意思来看，是基于物理的渲染技术（Physically Based Shading）。其实可以将其理解为一种光照模型，类似Lambert 或 Phong，只是更贴合现实，因为其高消耗，在最近的实时渲染中才成为可能。
PBS能实现更好的法线高光效果。</p>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">另一主要优势在于对用户更友好：使用一个Shader就能实现各种材质效果，且变换环境也无需频繁修改Shader参数，只需修改光照环境即可。</p>
<h4 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; font-size: 18px;"><span class="prefix" style="display: none;"></span><span class="content">5.正向渲染 vs 延迟渲染</span><span class="suffix" style="display: none;"></span></h4>
<section class="table-container" data-tool="mdnice编辑器" style="overflow-x: auto;"><table style="display: table; text-align: left;">
<thead>
<tr style="border: 0; border-top: 1px solid #ccc; background-color: white;">
<th style="font-size: 16px; border: 1px solid #ccc; padding: 5px 10px; font-weight: bold; background-color: #f0f0f0; min-width: 85px; text-align: center;"></th>
<th style="font-size: 16px; border: 1px solid #ccc; padding: 5px 10px; font-weight: bold; background-color: #f0f0f0; min-width: 85px; text-align: center;">正向渲染</th>
<th style="font-size: 16px; border: 1px solid #ccc; padding: 5px 10px; font-weight: bold; background-color: #f0f0f0; min-width: 85px; text-align: center;">延迟渲染</th>
</tr>
</thead>
<tbody style="border: 0;">
<tr style="border: 0; border-top: 1px solid #ccc; background-color: white;">
<td style="font-size: 16px; border: 1px solid #ccc; padding: 5px 10px; min-width: 85px; text-align: center;">顺序</td>
<td style="font-size: 16px; border: 1px solid #ccc; padding: 5px 10px; min-width: 85px; text-align: center;">先执行着色计算，再执行深度测试。</td>
<td style="font-size: 16px; border: 1px solid #ccc; padding: 5px 10px; min-width: 85px; text-align: center;">先执行深度测试，再执行着色计算。</td>
</tr>
<tr style="border: 0; border-top: 1px solid #ccc; background-color: #F8F8F8;">
<td style="font-size: 16px; border: 1px solid #ccc; padding: 5px 10px; min-width: 85px; text-align: center;">复杂度</td>
<td style="font-size: 16px; border: 1px solid #ccc; padding: 5px 10px; min-width: 85px; text-align: center;">正向渲染渲染 n 个物体在 m 个光源下的着色，复杂度为 O(n*m)次</td>
<td style="font-size: 16px; border: 1px solid #ccc; padding: 5px 10px; min-width: 85px; text-align: center;">延迟渲染渲染 n 个物体在 m 个光源下的着色，复杂度为 O(n+m)次</td>
</tr>
<tr style="border: 0; border-top: 1px solid #ccc; background-color: white;">
<td style="font-size: 16px; border: 1px solid #ccc; padding: 5px 10px; min-width: 85px; text-align: center;">光源</td>
<td style="font-size: 16px; border: 1px solid #ccc; padding: 5px 10px; min-width: 85px; text-align: center;">光源数量对计算复杂度影响巨大，所以比较适合户外这种光源较少的场景。</td>
<td style="font-size: 16px; border: 1px solid #ccc; padding: 5px 10px; min-width: 85px; text-align: center;">最大的优势就是将光源的数目和场景中物体的数目在复杂度层面上完全分开。 也就是说场景中不管是一个三角形还是一百万个三角形，最后的复杂度不会随 光源数目变化而产生 巨大变化。</td>
</tr>
</tbody>
</table>
</section><h2 data-tool="mdnice编辑器" style="margin-top: 30px; margin-bottom: 15px; padding: 0px; font-weight: bold; color: black; border-bottom: 2px solid#CBCAAF; font-size: 1.3em;"><span class="prefix" style="display: none;"></span><span class="content" style="display: inline-block; font-weight: bold; background: #CBCAAF; color: #ffffff; padding: 3px 10px 1px; border-top-right-radius: 3px; border-top-left-radius: 3px; margin-right: 3px;">References</span><span class="suffix"></span><span style="display: inline-block; vertical-align: bottom; border-bottom: 36px solid #efebe9; border-right: 20px solid transparent;"> </span></h2>
<ol data-tool="mdnice编辑器" style="margin-top: 8px; margin-bottom: 8px; padding-left: 25px; color: black; list-style-type: decimal;">
<li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;"><a href="https://blog.csdn.net/qq_29579137/article/details/84486003" style="text-decoration: none; word-wrap: break-word; font-weight: bold; color: #CBCAAF; border-bottom: 1px solid#CBCAAF;">https://blog.csdn.net/qq_29579137/article/details/84486003</a></section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;"><a href="https://zhuanlan.zhihu.com/p/79800363" style="text-decoration: none; word-wrap: break-word; font-weight: bold; color: #CBCAAF; border-bottom: 1px solid#CBCAAF;">https://zhuanlan.zhihu.com/p/79800363</a></section></li><li><section style="margin-top: 5px; margin-bottom: 5px; line-height: 26px; text-align: left; color: rgb(1,1,1); font-weight: 500;"><a href="https://blog.csdn.net/puppet_master/article/details/53900568" style="text-decoration: none; word-wrap: break-word; font-weight: bold; color: #CBCAAF; border-bottom: 1px solid#CBCAAF;">https://blog.csdn.net/puppet_master/article/details/53900568</a></section></li></ol>
<p data-tool="mdnice编辑器" style="font-size: 16px; padding-top: 8px; padding-bottom: 8px; margin: 0; line-height: 26px; color: black;">待续...</p>
</section>