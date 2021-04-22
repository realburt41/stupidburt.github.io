---
layout:     post
title:      "Unity-ShaderTutor"
subtitle:   "笔记"
date:       2021-4-11
author:     "Burt"
header-style: text 
tags:
    - Unity
    - Shader
---



## 零、渲染管线概述

![renderTarget](/img/in-post/ShaderTutor/renderTarget.png)

Unity Build-in Rendering Pipeline（渲染管线）

- CPU应用程序端的渲染逻辑
- GPU渲染管线（Graphic Pipeline）

![renderpipeline](/img/in-post/ShaderTutor/renderpipeline.png)

简单点就分为：

CPU应用程序端的渲染逻辑 - 》 GPU渲染管线 - 》 Frame Buffer

CPU应用程序端的渲染逻辑我们简称**应用程序阶段**

可分为：

- 剔除
  - 视锥体剔除（Frustum Culling）
  - 层级剔除（Layer Culling Mask）
  - 遮挡剔除（Occlusion Culling）等
- 排序
  - 渲染队列（Render Queue）
  - 不透明队列（Render Queue < 2500）：按摄像机**从前到后**排序
  - 半透明队列（Render Queue > 2500）：按摄像机**从后到前**排序
- 打包数据
  - Batch
    - 模型信息：
      - 顶点坐标
      - 法线
      - UV纹理坐标
      - 切线
      - 顶点色
      - 索引列表：就是模型顶点的位置
    - 变换矩阵：
      - 世界变换矩阵
      - VP矩阵：根据摄像机位置
      - 和fov等参数构建
    - 灯光、材质参数：
      - Shader
      - 材质参数
      - 灯光信息
- 绘制调用：也就是Draw Call

我们主要把重点放在**GPU渲染管线**上

![renderpipeline2](/img/in-post/ShaderTutor/renderpipeline2.png)

![renderpipeline3](/img/in-post/ShaderTutor/renderpipeline3.png)

GPU渲染管线可以分为四个内容：

- **顶点Shader**
  - 最重要的任务：将顶点坐标从模型空间变换到裁剪空间
  - 通俗的讲：模拟“拍照”的过程，模拟投影成像的过程，将我们的3D模型进行变形
  - 通过顶点Shader处理后，相机金字塔状的视锥体被转换变形成一个比例为2 x 2 x 1的立方体（CVV）
  - 顶点Shader并不会产生2D图像，仅使得场景中的3D对象产生变形效果
  - ![dingdian](/img/in-post/ShaderTutor/dingdian.png)
  
- 图元装配及光栅化

  - ![hardware](/img/in-post/ShaderTutor/hardware.png)

- **片元处理**
- 最重要的任务：上色
  - 纹理技术Texturing
    - 纹理采样
    - 纹理过滤机制：就是unity里的过滤模式（Filter Mode）
    - Mipmap
    - 纹理寻址模式：就是unity里的环绕模式（Wrap Mode）
    - 纹理压缩格式
  - 光照计算Lighting
    - 光照组成：
      - 直接光照
      - 间接光照
    - 光照模型：
      - Phone光照模型
      - 基本框架：
        - Direct Diffuse
        - Direct Specular
        - Indirect Diffuse
        - Indirect Specular
  
- 输出合并

  - 最重要的任务：处理遮挡关系、处理半透明混合
  - 通俗地讲：片元通过重重考验到达像素点位置的过程
  - 帧缓冲区：Frame Buffer
    - 颜色缓冲区：Color Buffer
    - 深度缓冲区：Depth Buffer
    - 模板缓冲区：Stencil Buffer
  - 深度测试：Depth Test
    - 深度写入：ZWrite
    - 深度测试：ZTest
  - 混合：Blending
    - 从后到前
    - 关闭ZWirte

总结：

![FinalRenderPipeline](/img/in-post/ShaderTutor/FinalRenderPipeline.png)





## 一、消融篇

效果：

![dissolve](/img/in-post/ShaderTutor/dissolve.gif)

实现了：

- 消融
- 消融边缘
- shader控制开关1：multi_compile



源码：

~~~glsl
Shader "Burt/1_Dissolve"
{
	Properties
	{
		_MainTex("MainTex", 2D) = "white" {}
		_AddColor("AddColor",Color) = (0,0,0,0)
		[NoScaleOffset]_DissolveTex("DissolveTex", 2D) = "white" {}
		[NoScaleOffset]_RampTex("RampTex", 2D) = "white" {}
		[Toggle]_DissolveEnable("DissolveEnable",int) = 0
		_ClipAmount("ClipAmount",Range(0,1)) = 0
		_EdgeAmount("EdgeAmount",Range(0,0.2)) = 0.1
	}
		SubShader
		{
			Tags { "RenderType" = "Opaque" }
			LOD 100

			Pass
			{
				CGPROGRAM
				#pragma vertex vert
				#pragma fragment frag
				
				//变体，作用类似开关，名字格式：_ Properties的变量名_ON。全大写
				#pragma multi_compile _ _DISSOLVEENABLE_ON

				#include "UnityCG.cginc"

				struct appdata
				{
					float4 vertex : POSITION;
					float2 uv : TEXCOORD0;
				};

				struct v2f
				{
					float2 uv : TEXCOORD0;
					float4 vertex : SV_POSITION;
				};

				sampler2D _MainTex;
				float4 _MainTex_ST;
				sampler2D _DissolveTex;
				sampler _RampTex;
				float4 _AddColor;
				float _ClipAmount;
				float _EdgeAmount;

				v2f vert(appdata v)
				{
					v2f o;
					o.vertex = UnityObjectToClipPos(v.vertex);
					o.uv = TRANSFORM_TEX(v.uv, _MainTex);
					//相当于：o.uv = v.uv * _MainTex_ST.xy + _MainTex_ST.zw;
					return o;
				}

				fixed4 frag(v2f i) : SV_Target
				{
					//sample the texture
					fixed4 col = tex2D(_MainTex, i.uv);
					fixed4 dis = tex2D(_DissolveTex, i.uv);
					
					col += _AddColor;
					
					//在这里判断要不要打开
					#if _DISSOLVEENABLE_ON	
						clip(dis.r - _ClipAmount);
						//小于0剔除，大于0不剔除.难以理解就想想现在这里是单个像素

						fixed disValue = saturate((dis.r - _ClipAmount) / _EdgeAmount);
						//此处是smoothstep方法的一部分
						
						fixed4 ramp = tex1D(_RampTex, disValue);
						//优化：
						//1、saturate取代smoothstep是因为saturate够用，而smoothstep比saturate费	
						//2、只要RampTex的x坐标，不用y坐标，所以1D

						//之前出现模型泛白bug是因为RampTex的wrap mode是repeat，改成clamp就没事了
						col += ramp;
					#endif
						
					return col;
				}
				ENDCG
			}
		}
}
~~~

1、方法

- clip()：小于0剔除，大于0不剔除
- saturate()：小于0返回0，大于1返回1，0~1之间则返回本身。注意：参数只有一个
- multi_compile _ 变量名_ON：全大写，注意第一个下划线后有一个空格
- smoothstep()：saturate()的平滑过渡版，平滑程度基于第三个值

2、优化

- 由于RampTex只用到x方向，所以为了减少计算量，用tex1D方法
- smoothstep()的柔滑曲线效果在这里作用不大，但是很耗资源，所以改用saturate



## 二、透明混合篇

效果：

![EffectUV](/img/in-post/ShaderTutor/EffectUV.gif)

实现了：

- 透明
- 遮罩
- 扰动
- shader控制开关2：shader_feature
- 时间控制



源码：

~~~glsl
Shader "Burt/2_EffectUV"
{
    Properties
    {
       [Header(RenderMode)]
       [Enum(UnityEngine.Rendering.BlendMode)]_SrcBlend("ScrBlend",int) = 0
       [Enum(UnityEngine.Rendering.BlendMode)]_DrcBlend("DcrBlend",int) = 0
       [Enum(UnityEngine.Rendering.CullMode)]_Cull("Cull",int) = 0
 
       [Header(MainTexture)]
       _MainTex("MainTex",2D) = "white" {}
       _UVSpeedX("UVSpeedX",float) = 0
       _UVSpeedY("UVSpeedY",float) = 0

       [Header(Mask)]
       [Toggle(MASKENABlE)]_MaskEnable("MaskEnable",int) = 0
       _MaskTex("MaskTex",2D) = "white" {}
       _MaskSpeedX("MaskSpeedX",float) = 0
       _MaskSpeedY("MaskSpeedY",float) = 0

       [Header(Distort)]
       [Toggle(DISTORTENABlE)]_DistortEnable("DistortEnable",int) = 0
       _DistortTex("DistortTex",2D) = "white" {}
       _Distort("Distort",Range(0,1)) = 0
       _DistortSpeedX("DistortSpeedX",float) = 0
       _DistortSpeedY("DistortSpeedY",float) = 0

       [Header(OtherSetting)]
       _AddColor("AddColor",Color) = (1,1,1,1)
       _Instensity("Intensity",Range(-4,4)) = 1


    }
    SubShader
    {
        Tags { "RenderType"="Transparent" "Queue" = "Transparent"}

        Blend [_SrcBlend] [_DrcBlend]
        Cull [_Cull]

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #pragma shader_feature DISTORTENABlE
            #pragma shader_feature MASKENABlE

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float4 vertex : SV_POSITION;
                float4 uv : TEXCOORD0;
                float2 uv2 : TEXCOORD1;
            };

            half _DistortEnable,_MaskEnable;
            sampler2D _MainTex; float4 _MainTex_ST;
            sampler2D _MaskTex; float4 _MaskTex_ST;
            sampler2D _DistortTex; float4 _DistortTex_ST;

            float _UVSpeedX,_UVSpeedY,_MaskSpeedX,_MaskSpeedY,_DistortSpeedX,_DistortSpeedY;

            fixed4 _AddColor;
            half _Instensity;
            half _Distort;


            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.uv.xy = TRANSFORM_TEX(v.uv,_MainTex) + float2(_UVSpeedX,_UVSpeedY) * _Time.y;
                
                #if MASKENABlE
                    o.uv.zw = TRANSFORM_TEX(v.uv,_MaskTex) 
                                + float2(_MaskSpeedX,_MaskSpeedY) * _Time.y;
                #endif

                #if DISTORTENABlE
                    o.uv2 = TRANSFORM_TEX(v.uv,_DistortTex) 
                                + float2(_DistortSpeedX,_DistortSpeedY) * _Time.y;
                #endif

                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                fixed2 disScale = i.uv.xy;

                #if DISTORTENABlE
                    fixed4 disTex = tex2D(_DistortTex,i.uv2);
                    disScale = lerp(i.uv.xy,disTex,_Distort);
                #endif

                fixed4 col = tex2D(_MainTex,disScale);
                col *= _AddColor * _Instensity;

                #if MASKENABlE
                    fixed4 mask = tex2D(_MaskTex,i.uv.zw);
                    col *= mask;
                #endif

      
                return col;
            }
            ENDCG
        }
    }
}
~~~

思路：

- 遮罩实现：首先打开blend，一般是 one one。mask图一般是黑白图，和主图融合，黑的地方就带走没了，白的地方保留，白与黑之间的灰就变成透明
- 扰动：先读取扰动图，然后定义disScale变量（作为主图的uv），赋值给disScale**原uv和扰动图之间**的**lerp值**
- shader_feature：和multi_compile大同小异
- 时间控制：_Time.xyzw：
  - x：t /20
  - y：t
  - z：t * 2
  - w：t * 3



shader_feature和multi_compile的区别：

使用multi_compile，是可以在运行时（真机上）用EnableKeyword和DisableKeyword动态改变材质，而shader_feature不行

shader_feature适合在材质中设置，而multi_compile 适合用代码全局设置



## 三、屏幕UV篇

效果：

![ScreenUV](/img/in-post/ShaderTutor/ScreenUV.gif)

实现了：

- 屏幕UV及优化
- 屏幕抓取



源码：

~~~glsl
Shader "Burt/3_ScreenUVDsitort"
{
    Properties
    {
        _DistortTex ("DistortTex", 2D) = "white" {}
        [Toggle(DISTORT)]_DistortEnable("DistortEnable",int) = 0
        _Distort("Distort",Range(0,1)) = 0
        _DistortSpeedX("DistortSpeedX",float) = 0
        _DistortSpeedY("DistortSpeedY",float) = 0

        [Toggle(REVERSE)]_ReverseEnable("ReverseEnable",int) = 0
    }
    SubShader
    {
        Tags { "Queue" = "Transparent" }

        //抓取屏幕，要定义名字，不然每多一个材质多一个GrabPass
        GrabPass{"_GrabTex"}

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #pragma shader_feature DISTORT
            #pragma shader_feature REVERSE

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float2 uv : TEXCOORD0;
                float4 pos : SV_POSITION;
                float4 screenUV :TEXCOORD1;
            };

            sampler2D _DistortTex,_GrabTex;//要和GrabPass定义的名字一样
            float4 _DistortTex_ST;
            float _Distort,_DistortSpeedX,_DistortSpeedY;
            
            half _DistortEnable,_ReverseEnable;

            v2f vert (appdata v)
            {
                v2f o;
                o.pos = UnityObjectToClipPos(v.vertex);
                o.screenUV = ComputeScreenPos(o.pos);//屏幕坐标通过这个方法获取

                #if DISTORT
                    o.uv = TRANSFORM_TEX(v.uv,_DistortTex) 
                            + float2(_DistortSpeedX,_DistortSpeedY) * _Time.y;
                #endif
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
                //最简屏幕坐标代码。_ScreenParams是屏幕分辨率
                //fixed2 screen = i.pos.xy / _ScreenParams.xy;
                //fixed2 disScale = screen;

                fixed4 disScale = i.screenUV;

                #if DISTORT
                    float4 disTex = tex2D(_DistortTex,i.uv);
                    disScale = lerp(i.screenUV,disTex,_Distort);
                    //假如使用最简屏幕坐标，这里lerp第一个参数要改成screen
                #endif

                //最简屏幕坐标代码
                //float4 grab = tex2D(_GrabTex,disScale);
                float4 grab = tex2Dproj(_GrabTex,disScale);//投影贴图采样

                #if REVERSE
                    grab = 1 - grab;
                #endif

                return grab;
            }
            ENDCG
        }
    }
}
~~~

方法及参数：

- GrabPass
- ComputeScreenPos()
- tex2Dproj()
- _ScreenParams



tex2Dproj和tex2D的区别：

tex2Dproj和tex2D这两个功能几乎相同。

唯一的区别是，在对纹理进行采样之前，`tex2Dproj`将输入的UV `xy`坐标除以其`w`坐标。这是将坐标从正交投影转换为透视投影。

**具体什么情况下使用tex2Dproj呢?**

我们知道,裁剪空间的坐标经过缩放和偏移后就变成了(0,ｗ),而当分量除以分量W以后,就变成了(0,1),这样在计算需要返回(0,1)值的时候,就可以直接使用tex2Dproj了.

Reference：**https://zhuanlan.zhihu.com/p/107627483**



最简屏幕坐标：

顶点函数（此时顶点是在裁剪空间下）传递到片元函数的**顶点**，假如在片元函数直接用，会被引擎或者硬件直接变成屏幕上的像素坐标，可以利用这个特性达成**最简屏幕坐标**





## 四、UI

PerRendererData：被他标签的属性在属性面板就看不到了，只能通过代码动态修改。他是配合setPropertyBlock()方法使用的，用于：想要A和B同样材质，分别不同属性的场景上，而且此操作会大大降低消耗





## 五、渲染路径

- 前向渲染路径(Forward Rendering Path) 
  - 特点：一个物体在受到多个灯光影响时，可能会产生一个或者多个Pass，具体取决于多个因素
  - 渲染方式：最亮的一盏平行灯和标记为Important的灯采用逐像素；一个灯被标记为Not Important，则这个灯采用逐顶点或者SH球谐；如果产生逐像素的灯数量小于工程中的像素灯数量的话，则会有更多的灯采用逐像素
    - 逐像素
    - 逐顶点
    - SH球谐函数
  - ForwardBase：仅用于一个**逐像素**的**平行灯**，以及所有的**逐顶点**与**SH**
  - ForwardAdd： 用于**其他所有的逐像素**灯
- 延迟渲染路径(Deferred Rendering Path)：将着色计算进行延后处理的一种渲染方式
  - 几何处理G-Buffer Pass：以下所有信息都会被存到几何缓冲区(G-Buffer)
    - RT0：ARGB32:Diffuse Color(RGB),Occlusion(A)
    - RT1：ARGB32:Specular Color(RGB),Roughness(A)
    - RT2：ARGB2101010:World Space Normal(RGB),Unused(A)
    - RT3：ARGB2101010(non-HDR) or ARGBHALF(HDR):Emission + Lighting + Lightmap + Reflection Probe
    - RT4：ARGB32:Light Occlusion
    - Depth + Stencil Buffer
  - 光照处理Lighting Pass：只需渲染出一个屏幕大小的二维矩形，使用第一步在G-Buffer中存储的数据对此矩阵的每一个片段进行计算光照
  - 优点：
    1. 影响一个物体的光源数量是没有限制的
    2. 每一个光源都是逐像素级别的效果，并且可以正确的计算法线贴图及阴影
  - 缺点：
    1. 不支持抗锯齿
    2. 不支持半透明效果
    3. 不支持正交相机
    4. 内存开销较大
  - 支持条件：
    1. 显卡必须支持Multiple Render Targets(MRT)，即多渲染目标
    2. ShaderModel在3.0及以上
    3. 手机平台在OpenGL3.0及以上
- 顶点照明渲染路径(Vertex Lit Rendering Path,legacy)
  - 优点
    1. 性能最优
    2. 支持的硬件最广
    3. 一个物体仅仅渲染一次，并且所有的光照计算都在顶点执行
  - 缺点
    1. 不支持像素级别的效果，比如阴影、高质量高光等
- 旧的延迟渲染路径(Deferred Rendering Path,legacy)

![rengdererPath](/img/in-post/ShaderTutor/rengdererPath.png)



Tags{ “LightMode” = “XXX” }

- Always：默认设置，任何情况下都会渲染，没有灯光信息
- ForwardBase：前向渲染中的基础Pass
- ForwardAdd：前向渲染中的额外Pass
- Dederred：延迟渲染
- ShadowCaster：渲染对象的shadowmap或者depthTexture
- MotionVectors：用于计算物体的MotionVectors
- PrepassBase：用于旧的延迟渲染
- PrepassFinal：用于旧的延迟渲染
- Vertex：用于顶点光照渲染（当物体没有光照贴图时）
- VertexLMRGBM：用于顶点光照渲染（PC与主机平台下，当物体有光照的时候）



## 六、深度测试

- 从3D投影为2D，变换后的Z值就成了深度信息
- 每个顶点的深度信息，在光栅化阶段会被插值，并写入到深度缓冲中（如果开启了深度写入的话）





## 七、纹理

**1、分类**

- 颜色纹理：一维纹理、二维纹理、三维纹理、立方体纹理
- 几何纹理：凹凸纹理、视差纹理、置换纹理、法线纹理



**2、纹理管线**

![textureLine](/img/in-post/ShaderTutor/textureLine.png)



**3、纹理作用**

颜色、高光、透明、凹凸



**4、Mipmap**

可以理解为纹理的LOD

![mipmapSetting](/img/in-post/ShaderTutor/mipmapSetting.png)

开启后，会生成不同层次贴图根据距离远近使用

![mipmap](/img/in-post/ShaderTutor/mipmap.png)

缺点：

开启后，引擎会将这些图一起打包进显存，所以这张图在显存的里的大小就多了30%。

比如你的图1m，那么打包进显存的图就有1.3m



优点：

- 增加缓存命中率，减少像素抖动感（因为相机离得远，模型太小，uv不好确定）
- 可配合质量设置来分级加载，减少不同配置下的内存



shader开启mipmap：

~~~glsl
Shader "Burt/10_Mipmap"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
        [IntRange]_MipmapRange("MipmapRange",Range(0,10)) = 0 
    }
    SubShader
    {
        Tags { "RenderType"="Opaque" }
        LOD 100

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float2 uv : TEXCOORD0;
                float4 vertex : SV_POSITION;
            };

            sampler2D _MainTex; float4 _MainTex_ST;
            half _MipmapRange;

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
                o.uv = TRANSFORM_TEX(v.uv, _MainTex);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {

                fixed4 mipmapUV = fixed4(i.uv,0,_MipmapRange);
                fixed4 col = tex2Dlod(_MainTex, mipmapUV);
                return col;
            }
            ENDCG
        }
    }
}
~~~



**5、纹理过滤**

事实上没有一个纹理上的纹素是与屏幕上的像素是一一对应的

![textureFilter](/img/in-post/ShaderTutor/textureFilter.png)

- Point(no filter)：不进行过滤，只采样离采样点最近的那一个纹素
  - ![noFilter](/img/in-post/ShaderTutor/noFilter.png)
- Bilinear：双线性过滤。在采样点周围采样4个纹素，并进行加权平均得出最后的值（离采样点越近的纹素权重越高）
  - ![BilinearFilter](/img/in-post/ShaderTutor/BilinearFilter.png)
- Trilinear：三线性过滤。在双线性过滤的基础上再加上对Mipmap的远近级别过滤。采样八个纹素，然后进行插值



**6、纹理的环绕模式**

![WrapMode](/img/in-post/ShaderTutor/WrapMode.png)

我们也可以在shader里面自己定义环绕模式：

~~~glsl
fixed4 frag (v2f i) : SV_Target
{
	#if _WRAPMODE_REPEAT
		i.uv = frac(i.uv);
	#elif _WRAPMODE_CLAMP
    	//方法一
    	//i.uv = clamp(i.uv,0,1);
    	//方法二
    	i.uv = saturate(i.uv);
	#endif

	fixed4 mipmapUV = fixed4(i.uv,0,_MipmapRange);
	fixed4 col = tex2Dlod(_MainTex,mipmapUV);
	return col;
}
~~~



**6、立方体纹理（Cubemap）**

两种生成方式：

- ![cubemap1](/img/in-post/ShaderTutor/cubemap1.png)
- ![cubemap2](/img/in-post/ShaderTutor/cubemap2.png)



采样方式：

![cubemap3](/img/in-post/ShaderTutor/cubemap3.png)

环境映射：

![cubemap4](/img/in-post/ShaderTutor/cubemap4.png)

核心代码：

~~~glsl
Properties
{
        _Cubemap("Cubemap",Cube) = "white"{}
}

samplerCUBE _Cubemap;

fixed4 frag (v2f i) : SV_Target
{
	//Cubemap映射
	fixed3 N = normalize(i.worldNormal);
	fixed3 V = normalize(UnityWorldSpaceViewDir(i.worldPos));

	fixed3 R = reflect(-V,N);

	col = texCUBE(_Cubemap,R);

	return col;
}
~~~

对反射探针的支持：

首先反射探针烘焙出来，记得要烘焙进的对象要设置为Static

然后就是shader支持：

~~~glsl
fixed4 frag (v2f i) : SV_Target
{
	//Cubemap映射
	fixed3 N = normalize(i.worldNormal);
	fixed3 V = normalize(UnityWorldSpaceViewDir(i.worldPos));

	fixed3 R = reflect(-V,N);

	//col = texCUBE(_Cubemap,R);

	//return col;

	//反射探针支持
	col = UNITY_SAMPLE_TEXCUBE(unity_SpecCube0,R);
	half3 skyColor = DecodeHDR(col,unity_SpecCube0_HDR);

	return fixed4(skyColor,1);
}
~~~



## 八、GPU Instance

**1、Rendering Statistics Window面板参数**

![status](/img/in-post/ShaderTutor/status.png)

Audio就不看了

Graphics：

- FPS：注意，这个数字只包括做帧更新和渲染游戏视图所花费的时间;它不包括在编辑器中绘制Scene视图、Inspector和其他只针对编辑器的处理所花费的时间
- CUP：main 0.9ms（主线程）
- render thread：GPU渲染线程处理图像所花费的时间，具体数值由GPU性能来决定
- Batch：**批处理**是指引擎试图将多个对象的渲染合并到一块内存中，以减少由于资源切换造成的CPU开销，Batches就是合并后的DrawCall次数
- Saved by batching：合并的批次数。为了确保良好的批处理，您通常应该尽可能在不同对象之间共享材质。更改渲染状态将会将批次破坏分解为具有相同状态的组
- Tris：三角面数
- Verts：顶点数
- Screen：屏幕分辨率、它的抗锯齿水平以及在显存中占用的大小
- SetPass calls：每次GPU切换一个Pass前，都会产生一次SetPassCall
- Shadow casters：表示场景中有多少个可以投射阴影的物体，一般这些物体都作为场景中的光源。
- Visible Skinned Meshes：所渲染的蒙皮网格的数量
- Animations：播放的动画数量

补充：DrawCall：CPU每次调用图形API命令GPU渲染的操作



**2、合批（Batching）**

- **动态合批**：每一帧把可以进行批处理的模型网格进行合并，再把合并后的模型数据传递给GPU，然后使用同一个材质进行渲染。经过动态批处理的物体仍然可以移动，这是由于在处理每帧时Unity都会重新合并一次网格。多Pass的shader会中断批处理，在前向渲染中，我们有时需要使用额外的Pass为模型添加更多光照效果，这样模型就不会被批处理了
  - 材质相同是合批的前提，但如果是材质实例的话，则一样无法合批
  - 支持不同网格的合批
  - 单个网格最多300个顶点，900个顶点属性（顶点属性的上限可能未来版本会调整）
    - 如果Shader中用到了网格的position、normal和uv的话，则最多是300个顶点
    - 如果Shader中用到了网格的position、normal、uv0、uv1和tangent的话，则最多是180个顶点
  - 镜像的transform无法合批（就是A的Scale是1，然后B的Scale是-1，就造成了镜像的transform）
- **静态合批**：它的实现原理是，只在运行开始阶段，把需要进行静态批处理的模型合并到一个新的网格结构中，这意味着这些模型不可以在运行时刻被移动。由于他只需要进行一次合并操作，因此比动态批处理更加高效。静态批处理需要占用更多的内存来存储合并后的几何结构
  - 所有Mesh实例具有相同的材质引用
  - 所有Mesh必须标记为Static。做完这两件事静态合批就做好了（原理：会在内部重新合并所以的网格生成一个Mesh，占用内存的空间）
  - 静态合批和Shader没有关系
  - 显存额外的内存占用
  - 静态对象无法通过原始的Transform移动
  - 注意：像大规模的一个城市地图，不能用静态合批，因为如果只看到一个角落，但是处理器却把所以的城市都渲染出来了，占用了过多内存空间（看到的当前物体，所有和他与之合并的也会渲染，无论是否在摄像机的范围内。解决办法使用遮挡剔除）。总结就是：**任何一点可见，全部渲染**
  - 场景
    - 小场景：重复不多。美术在建模的时候把场景合并一起，这个时候导入静态合批会减少占用。优化好
    - 大场景：外部大部分是由动态合批（在不停的移动，显示的东西实时变化，原本占用的内存就高，静态后就会更高），局部小房间可以使用静态合批。大规模静态合批性价比不高



**3、GPU实例化（GPU Instance）**

与合批不同的是，GPU实例化要求网格、材质是一样的，材质属性可以不一样。

而且也不是所有平台都支持，比如手机端必须要OpenGL ES3.0以上才支持

获得GPU实例化需要

- 硬件API支持

- Shader支持

  - 在两个结构体里定义instanceID

    ~~~glsl
    UNITY_VERTEX_INPUT_INSTANCE_ID
    ~~~

  - 声明常量寄存器，并且在其中存储需要的变量属性

    ~~~glsl
    UNITY_INSTANCING_BUFFER_START(prop)
    UNITY_DEFINE_INSTANCED_PROP(fixed4, _Color)	//例如传入Color
    UNITY_INSTANCING_BUFFER_END(prop) 
    ~~~

  -  在顶点着色器中设置相应的instanceID,默认的坐标变换就已经支持了

    ~~~glsl
    UNITY_SETUP_INSTANCE_ID(v);
    ~~~

  - 把instanceID从顶点着色器传到片断着色器

    ~~~glsl
    UNITY_TRANSFER_INSTANCE_ID(v, o);
    ~~~

  - 在片断着色器中设置相应的instanceID

    ~~~glsl
    UNITY_SETUP_INSTANCE_ID(i);
    ~~~

  - UNITY_ACCESS_INSTANCED_PROP,调用常量寄存器中的属性值

    ~~~glsl
    return UNITY_ACCESS_INSTANCED_PROP(prop, _Color)
    ~~~

  - 源码：

    ~~~glsl
    Shader "Burt/GPUInstance"
    {
        Properties
        {
            _Color("Color",Color) = (1,1,1,1)
        }
        SubShader
        {
            Tags { "RenderType"="Opaque" }
    
            Pass
            {
                CGPROGRAM
                #pragma vertex vert
                #pragma fragment frag
                #pragma multi_compile_instancing
    
                #include "UnityCG.cginc"
    
                struct appdata
                {
                    float4 vertex : POSITION;
                    float2 uv : TEXCOORD0;
                    //在顶点着色器的输入定义instanceID
                    UNITY_VERTEX_INPUT_INSTANCE_ID
                };
    
                struct v2f
                {
                    float2 uv : TEXCOORD0;
                    float4 pos : SV_POSITION;
                    float3 wPos:TEXCOORD4;
                    //在顶点着色器的输出定义instanceID
                    UNITY_VERTEX_INPUT_INSTANCE_ID
                };
    
                //声明常量寄存器，并且在其中存储需要的变量属性
                UNITY_INSTANCING_BUFFER_START(prop)
                UNITY_DEFINE_INSTANCED_PROP(fixed4, _Color)
                UNITY_INSTANCING_BUFFER_END(prop) 
    
                v2f vert (appdata v)
                {
                    v2f o;
                    //在顶点着色器中设置相应的instanceID,默认的坐标变换就已经支持了
                    UNITY_SETUP_INSTANCE_ID(v);
                    //把instanceID从顶点着色器传到片断着色器
                    UNITY_TRANSFER_INSTANCE_ID(v, o);
                    o.pos = UnityObjectToClipPos(v.vertex);
                    o.wPos = mul(unity_ObjectToWorld,v.vertex);
                    o.uv = v.uv;
                    return o;
                }
    
                fixed4 frag (v2f i) : SV_Target
                {
                    //在片断着色器中设置相应的instanceID
                    UNITY_SETUP_INSTANCE_ID(i);
                    //UNITY_ACCESS_INSTANCED_PROP,调用常量寄存器中的属性值
                    return i.wPos.y*0.15+UNITY_ACCESS_INSTANCED_PROP(prop, _Color);
                }
                ENDCG
            }
        }
    }
    ~~~

    

- 脚本支持

  - 定义MaterialPropertyBlock变量a

  - a.SetColor

  - target.GetComponentInChildren<Renderer>().SetPropertyBlock(a)

  - 源码：

    ~~~c#
    using System.Collections;
    using System.Collections.Generic;
    using UnityEngine;
    
    public class GPUInstanceScript : MonoBehaviour
    {
        public GameObject prefabs;
    
        public int acount;
    
        public int range;
    
        private MaterialPropertyBlock propertyBlock;
    
        // Start is called before the first frame update
        void Start()
        {
    
            if (propertyBlock == null)
                propertyBlock = new MaterialPropertyBlock();
    
            for (int i = 0;i< acount; i++)
            {
                Vector3 newRange = new Vector3(Random.insideUnitSphere.x * range, 0, Random.insideUnitSphere.z * range);
                GameObject go = Instantiate(prefabs, newRange, prefabs.transform.rotation);
    
                Color newColor = new Color(Random.value, Random.value, Random.value);
    
                propertyBlock.SetColor("_Color", newColor);
    
                go.GetComponentInChildren<Renderer>().SetPropertyBlock(propertyBlock);
    
            }
        }
    }
    ~~~

    



## 九、优化

对于一个游戏来说，它主要需要使用两种计算资源，CPU和GPU，他们会互相合作，来让我们游戏可以在预期的帧率和分辨率下工作。
所以，我们可以把造成游戏性能瓶颈的主要原因分成以下几个方面

- CPU
  - 过多的draw call
  - 复杂的脚本或者物理模拟
- GPU
  - 顶点处理
    - 过多的顶点
    - 过多的逐顶点计算
  - 片元处理
    - 过多的片元：这部分优化重点在于减少overdraw，**overdraw就是同一个像素被绘制了多次**
    - 过多的逐片元计算
- 带宽
  - 使用了尺寸很大且未压缩的纹理
  - 分辨率过高的帧缓存



对于CPU来说，限制它的主要是每一帧中draw call的数目

draw  call简单来说就是CPU在每次通知GPU进行渲染之前，都需要提前准备好顶点数据（如位置，法线，颜色，纹理坐标等），然后调用一系列API把它们放到GPU可以访问的指定位置，最后调用一个绘制命令，而调用绘制命令时，就会产生一个draw call

过多的draw call会造成CPU的性能瓶颈

对于GPU来说，它负责整个渲染流水线，它从处理CPU传递过来的模型数据开始，进行顶点着色器，片元着色器等一系列工作，最后输出屏幕上的每个像素，因此CPU的性能瓶颈和需要处理的顶点数目，屏幕分辨率，显存等因素有关

相关的优化策略可以从减少处理的数据规模（包括顶点数目和片元数目），减少运算复杂度等方面入手

相对应的，就衍生出很多优化技术

- CPU优化
  - 使用批处理技术减少draw call数目
- GPU优化
  - 减少需要处理的顶点数目
  - 优化几何体
  - 使用遮挡剔除技术
  - 使用模型的LOD（多细节层次）技术
  - 减少需要处理的片元数目
  - 控制绘制顺序
  - 警惕透明物体：如果要得到正确的渲染结果，就必须从后往前渲染，就意味着，半透明物体几乎一定会造成overdraw
  - 减少实时光照和阴影
    - 实时光照有可能提高draw call和overdraw，因为对于逐像素光源来说，被这些光源照亮的物体需要再渲染一次，无论是静态批处理还是动态批处理，对于这种额外处理逐像素光源的Pass都无法进行批处理，他们会中断批处理
    - 游戏中往往使用了烘焙技术，把光照提前烘焙到一张光照纹理中，然后运行是只需要根据纹理采样得到光照结果即可
    - 在移动平台上，一个物体使用的逐像素光源数目应小于1（不包括平行光），如果一定要使用更多实时光照，可以用逐顶点光照替代。阴影的处理同理。
  - 减少计算复杂度
  -  使用Shader的LOD技术
  - 代码方面的优化
- 节省内存带宽
  - 减少纹理大小
  - 利用分辨率缩放



**1、精度优化**

- 三种精度（从低到高）：
  - fixed：普通纹理、颜色类；8位
  - half：HDR颜色、方向向量；16位
  - float：位置坐标、纹理坐标；32位

实际上，使用的精度取决于目标平台和GPU

现在桌面级GPU都是直接采用float，Shader中的fixed/half/float最后都是用的float

现代移动端GPU大多仅支持half和float，所以能用half的就用half

fixed仅用于较旧的移动GPU



**2、能放顶点的计算就不要放片断中**



**3、少用多Pass**

一个subShader内如果含有多个Pass（不含Unity中特殊作用的），则会渲染多次，同时不能进行合批



**4、小心使用AlphaTest和ColorMask**

- shader中使用了clip()函数通常在大多数平台上使用**AlphaTest**会有性能优势，但是在IOS和某些使用PowerVR GPU的Android设备上性能很低
- **ColorMask**在IOS和部分Android设备上同样性能很低



**5、NoScaleOffset**

~~~glsl
[NoScaleOffset]_MainTex("MainTex", 2D) = "white" {}
~~~

在不需要调节Tilling和Offset的贴图中加入[NoScaleOffset]，同时在shader不要做相关计算



**6、DiableBatching**

~~~glsl
Tags{"DiableBatching" = "true"}
~~~

true表示不进行合批

false表示能合批就合批，默认值

该指令同时影响动态合批和静态合批

如果顶点上的计算需要在模型的本地空间下进行，则需要开启，否则最好不要开启



**7、GrabPass**

消耗比较大

GrabPass如果不指定贴图名称，则每个对象的GrabPass都会每帧生成一次

GrabPass如果指定贴图名称，则所有对象的GrabPass在一帧内只会生成一次



**8、Overdraw**

Overdraw是重复绘制的意思，表示屏幕上的某个像素被绘制了多次，多发生在半透明物体叠加时



**9、变体优化**

变体的数量直接影响Shaderlab内存的占用，能少则少

尽量不要用内置的Standard材质，会生成大量的变体，可以自己修改定制一个

变体不是你加一条就多一条，而是你每加一条，就会有你新加的一条与旧变体的组合，例如：

~~~glsl
#pragma multi_compile A
//A是旧的，B是新加的
#pragma multi_compile B
~~~

最后编译出来的变体就是：

~~~glsl
A
B
A B
~~~

不是两个而是三个，更多的数量以此类推

- 第一种优化方式：自己从官方有的定义自己需要的
- 第二种优化方式：skip掉不需要的

**尽量少用multi_compile**

**尽量用shader_feature**，shader有个指令是skip unused shader_feature，可以通过这个来减少变体。这样做该变体被使用才激活；如果一个都没激活，那么就仅有最新定义的被激活。而multi_compile不在skip的范围

假如要用程序动态修改，那只能用multi_compile

总结：

- 美术资源开关：shader_feature
- 程序动态修改：multi_compile



变体收集器（Shader Variant Collection）：

假如没有**变体收集器**，程序修改变体需要动态编译，假如变体太多会造成卡顿；所以事先用**变体收集器**收集起来吗，在加载时加进内存，这样就不用动态编译，直接拿过来用即可



**10、Shader Model**

由微软提出，要求显卡厂商按SM级别提供对应的功能与指令支持

不同的SM包含不同的指令集与Shader规范

高版本的SM是低版本的超集

例如：

~~~glsl
#pragma target 3.0	//shader编译目标级别
#pragma require xxx	//表明shader需要的特性功能
~~~

详细去看shader参考大全



**11、共享材质**

如果两个材质之间只有使用的纹理不同，我们可以把这些纹理合并到一张更大的纹理中，这张更大的纹理叫做图集（Atlas），一旦使用了同一张纹理，我们就可以使用同一个材质，再使用不同的采样坐标对纹理采样即可。

有时不同的物体在材质上还有一些微小的参数变化，例如颜色不同，某些浮点属性不同，但是，不管动态批处理还是静态批处理，他们前提都是要使用同一个材质，也就是说**他们指向的材质必须是同一个实体**。

我们可以把想要调整的参数存入网格顶点数据，例如，森林场景中所有的树使用了同一种材质，我们希望他们可以通过批处理来减少draw call，但是不同树的颜色可能不同，这是我们可以使用网格的顶点颜色数据进行调整。



**12、移动设备优化要点**

- 最小的Draw call
  - 只要有任何一次Materia或者Shader发生改变那么就会触发Drawcall触发渲染状态（Render State）的改变----》（如果有几十个个箱子但是他们的材质和Shader一样那么只会触发一次Drawcall）
  - 尽可能的合并材质贴图和Shader的使用，降低Drawcall
- 最小的材质数量
- 最下的纹理尺寸
- 方形&POT纹理
- Shader中尽可能低的数据类型
- 避免AlphaTest







## 十、PBR

Physically Based Rendering，中文译为基于物理的渲染

PBR是一种渲染方式，是使用基于物理原理和微平面理论的光照模型，以及使用从现实中测量的表面参数来准确表示真实世界材质的渲染理念

PBR是一系列技术的集合，包含GI、PBS等



**1、PBS（Physically Based Shadering）**

中文译为基于物理的着色

PBS是为了对光和材质之间进行更加真实的建模，实质上讲解基于物理的渲染本质就是PBS

核心理论：

- 物质的光学特性（Substance Optical Properties）
  - 现实世界中的物质根据导电性可分为三大类：绝缘体、半导体、导体
  - 渲染领域中大多分为两大类：导体（金属）、绝缘体（非金属）
  - 金属
    - 具有很高的反射率（>=0.5）
    - 会立即吸收任何折射光，因此金属不会出现任何次表面散射和透明效果
    - 所有可见颜色都来自反射
    - ![metal](/img/in-post/ShaderTutor/metal.png)
  - 非金属：
    - 具有很低的反射率（<=0.06）
    - 产生高光反射与漫反射现象
    - 非金属的高光反射为单色/灰色
    - ![nonMetal](/img/in-post/ShaderTutor/nonMetal.png)
- 微平面理论（Microfacet Theory）
  - 现实世界的表面大多都不是光学平滑的，这种微观几何上的变化会导致每个表面点对光有不同的反射和折射
  - 所以基于物理渲染的PBS技术都是基于微平面理论的，它假想任何平面都是由微平面组成的，根据这些微平面粗糙程度的不同，采用粗糙度贴图或者高光度贴图来进行表示
  - 一个平面越是粗糙，这个平面上的微平面排列就越是混乱
- 能量守恒（Energy Conservation）
  - 出射光线的能量永远不能超过入射光线的能量（自发光除外）
  - 也就是：镜面反射 + 漫反射 <= 入射光
  - 随着粗糙度的上升，镜面反射区域的面积会增加，基于能量守恒，故镜面反射区域的亮度则会降低
- 菲涅尔反射：
  - 是一种表示看到光线的反射率与视角相关的现象
  - 也就是：光源入射方向与平面法线方向夹角的对应关系。
  - 夹角越大，反射越大，亮度也越大
  - 反之夹角越小，反射就越小，亮度也就越小
- 线性空间光照：为了保证光照渲染的正确性，所以最好是在线性（linear）空间中进行操作与计算，这样才能尽最大的还原现实世界中光与物质的交互
  - ![space](/img/in-post/ShaderTutor/space1.png)





**2、双向反射分布函数BRDF**

这是一个渲染方程，描述了光能在场景中的流动，根据光的物理学原理，渲染方程可以完美的模拟出符合物理光学的结果

$L_{o} =L_{e} + \int_{Ω} f_{r} * L_{i} *(w_{i}*n) * dw_{i} $

其中：

- L<sub>o</sub> 是当前点的出射光亮度
- L<sub>e</sub> 是当前点的自发光亮度
- f<sub>r</sub> 是当前点的入射方向到出射方向光的反射比例，为BxDF
- L<sub>i</sub> 是当前点的入射光角度
- (w<sub>i</sub> * n) 是入射角带来的入射光衰减
- $\int_{Ω}...dw_{i}$ 是入射方向半球的积分，可以理解为无穷小的累加和



![BSSRDF](/img/in-post/ShaderTutor/BSSRDF.png)

其中：

- BSSRDF：双向表面散射反射分布函数
- BRDF：双向反射分布函数。也就是上图负责黑块上面的区域的函数，是这一节学习的重点
- BTDF：双向透射分布函数。也就是上图负责黑块下面的区域的函数
- BSDF：双向散射分布函数。BSDF = BRDF + BTDF



**迪斯尼原则的BRDF**

核心理念：

- 使用直观的参数，而不是晦涩的物理类参数
- 参数尽可能的少
- 参数在其合理范围内应该为0-1
- 允许参数在有意义的情况下超出正常的范围
- 所有参数组应尽可能的健壮与合理   



参数：

![desiny](/img/in-post/ShaderTutor/desiny.png)

其中：

1. BaseColor（固有色，图中没有，这是最基础的属性）：表面的颜色，通常由纹理提供
2. Subsurface（次表面）：使用次表面近似的控制漫反射形状
3. Metallie（金属度）：0 = 非金属，1 = 金属，这是两种不同模型之间的线性混合
4. Specular（镜面反射强度）：镜面反射的强度
5. SpecularTint（镜面反射颜色）：对美术控制的让步，用于对BaseColor的入射镜面反射进行颜色控制
6. Roughness（粗糙度）：表面粗糙度，控制漫反射和镜面反射
7. Anisotropic（各向异性强度）：用于控制镜面反射高光的纵横比，0 = 各向同性，1 = 各向异性
8. Sheen（光泽度）：一种额外的掠射分量，主要用于布料
9. SheenTint（光泽颜色）：对Sheen的颜色控制
10. ClearCoat（清漆强度）：特殊用途的第二个镜面波瓣
11. ClearCoatCloss（清漆光泽度）：控制透明涂层光泽度



**Gamma与Linnear**

因为：

![spcae2](/img/in-post/ShaderTutor/space2.png)

所以：

![space3](/img/in-post/ShaderTutor/space3.png)

有了最左边的校正，结果：

![space4](/img/in-post/ShaderTutor/space4.png)



---

to be continued


