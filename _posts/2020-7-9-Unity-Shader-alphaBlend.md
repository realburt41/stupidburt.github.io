<<<<<<< HEAD
---
layout:     post
title:      "Burtの奇妙冒险（一）"
subtitle:   "今天是遇到的是透明度混合！"
date:       2020-7-11
author:     "Burt"
header-img: "img/in-post/Camera/Camera-bg.jpg"
tags:
    - Unity
    - Shader
---





#### 遭遇战

今天遇到了一个粉Cube，他说我不小心把他自己的Shader弄丢了，问我能不能找回来，找不回来就揍我

我问他你的Shader是干嘛的，他说可以控制隐身

我又问他，是慢慢隐身还是突然隐身呀，他说慢慢慢慢的

我找东西不厉害，我就帮你做一个出来吧。于是他感动的亲了我一口

#### 准备

改变Shader的Tag，告诉Shader他是透明物体，并改变渲染顺序

~~~c
 Tags { "RenderType"="Transparent" "Queue" = "Transparent"}
~~~



涉及隐身（透明）就要关闭深度测试，不然就看不到隐身Cube后面的东西了

~~~c
ZWrite Off //关闭深度测试
~~~

慢慢隐身就不是alpha test，而是alpha blend

~~~c
blend SrcAlpha OneMinusSrcAlpha //传统透明度
~~~

好了，准备就绪，冲啊！



#### 主体

今天主题是透明度，其他的关于diffuse和maintex就不写了

其实透明度重点在准备那里，主体只需要在Fragment Shader控制Alpha值就行了

~~~c
fixed4 frag (v2f i) : SV_Target
{
	....
	return fixed4(diffuse,_AlphaScale);	//_AlphaScale控制透明度
}
~~~

然后给粉Cude套上去，他开心的跟我道谢，我一脚给他，告诉他以后不要亲我



#### 改进

然鹅这样的透明混合有点奇怪，看不到Cube内部的细节。所以为了增加Cube内部细节，需要两个Pass，一个渲染外面，一个渲染里面，本来需要两套贴图，但是为了偷懒就只用一套



粉Cube被我扒了衣服，孤零零的躲在角落哭泣



第一个Pass渲染内部，剔除外面，其余代码一样

~~~c
Cull Front
~~~

第二个Pass渲染外部，剔除后面，其实不写这个Cull也没关系，不写默认就是Back

~~~c
Cull Back
~~~

这一次再给粉Cube穿上，他问我为啥能看到他的内脏，还给他的内脏加了个图案

别问，问就是装逼







#### 完整代码

（以便我Copy）

~~~c
Shader "Burt/Alpha"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
		_AlphaScale("Alpha Scale",Range(0,1)) = 1
    }
    SubShader
    {
        Tags { "RenderType"="Transparent" "Queue" = "Transparent"}

        Pass
        {

			Cull Front

			ZWrite Off
			blend SrcAlpha OneMinusSrcAlpha

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"
				#include "Lighting.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
				float3 normal : NORMAL;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float2 uv : TEXCOORD0;
				float3 worldNormal : TEXCOORD1;
				fixed3 worldPos : TEXCOORD2;
                float4 vertex : SV_POSITION;
            };

            sampler2D _MainTex;
            float4 _MainTex_ST;
			float _AlphaScale;

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
				o.worldNormal = UnityObjectToWorldNormal(v.normal);
				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
                o.uv = TRANSFORM_TEX(v.uv, _MainTex);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
				fixed3 worldNormal = normalize(i.worldNormal);
				fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));
				
                // sample the texture
                fixed3 col = tex2D(_MainTex, i.uv).rgb;
				
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;

				fixed3 diffuse = _LightColor0.rgb * col * (max(0, dot(worldNormal, worldLightDir)) * 0.5 + 0.5) + ambient;
				

                return fixed4(diffuse,_AlphaScale);
            }
            ENDCG
        }



		Pass
        {

			Cull Back

			ZWrite Off
			blend SrcAlpha OneMinusSrcAlpha

            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"
				#include "Lighting.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
				float3 normal : NORMAL;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float2 uv : TEXCOORD0;
				float3 worldNormal : TEXCOORD1;
				fixed3 worldPos : TEXCOORD2;
                float4 vertex : SV_POSITION;
            };

            sampler2D _MainTex;
            float4 _MainTex_ST;
			float _AlphaScale;

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
				o.worldNormal = UnityObjectToWorldNormal(v.normal);
				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
                o.uv = TRANSFORM_TEX(v.uv, _MainTex);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
				fixed3 worldNormal = normalize(i.worldNormal);
				fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));
				
                // sample the texture
                fixed3 col = tex2D(_MainTex, i.uv).rgb;
				
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;

				fixed3 diffuse = _LightColor0.rgb * col * (max(0, dot(worldNormal, worldLightDir)) * 0.5 + 0.5) + ambient;
				

                return fixed4(diffuse,_AlphaScale);
            }
            ENDCG
        }
    }
}

~~~





#### 效果

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





Reference：《Shader入门精要》
=======
---
layout:     post
title:      "Burtの奇妙冒险（一）"
subtitle:   "今天是遇到的是透明度混合！"
date:       2020-7-11
author:     "Burt"
header-img: "img/in-post/Camera/Camera-bg.jpg"
tags:
    - Unity
    - Shader
---





#### 遭遇战

今天遇到了一个粉Cube，他说我不小心把他自己的Shader弄丢了，问我能不能找回来，找不回来就揍我

我问他你的Shader是干嘛的，他说可以控制隐身

我又问他，是慢慢隐身还是突然隐身呀，他说慢慢慢慢的

我找东西不厉害，我就帮你做一个出来吧。于是他感动的亲了我一口

#### 准备

改变Shader的Tag，告诉Shader他是透明物体，并改变渲染顺序

~~~c
 Tags { "RenderType"="Transparent" "Queue" = "Transparent"}
~~~



涉及隐身（透明）就要关闭深度测试，不然就看不到隐身Cube后面的东西了

~~~c
ZWrite Off //关闭深度测试
~~~

慢慢隐身就不是alpha test，而是alpha blend

~~~c
blend SrcAlpha OneMinusSrcAlpha //传统透明度
~~~

好了，准备就绪，冲啊！



#### 主体

今天主题是透明度，其他的关于diffuse和maintex就不写了

其实透明度重点在准备那里，主体只需要在Fragment Shader控制Alpha值就行了

~~~c
fixed4 frag (v2f i) : SV_Target
{
	....
	return fixed4(diffuse,_AlphaScale);	//_AlphaScale控制透明度
}
~~~

然后给粉Cude套上去，他开心的跟我道谢，我一脚给他，告诉他以后不要亲我



#### 完整代码

（以便我Copy）

~~~c
Shader "Burt/AlphaBlend"
{
    Properties
    {
        _MainTex ("Texture", 2D) = "white" {}
		_AlphaScale("Alpha Scale",Range(0,1)) = 1
    }
    SubShader
    {
        Tags { "RenderType"="Transparent" "Queue" = "Transparent"}

		ZWrite Off
		blend SrcAlpha OneMinusSrcAlpha

        Pass
        {
            CGPROGRAM
            #pragma vertex vert
            #pragma fragment frag

            #include "UnityCG.cginc"
				#include "Lighting.cginc"

            struct appdata
            {
                float4 vertex : POSITION;
				float3 normal : NORMAL;
                float2 uv : TEXCOORD0;
            };

            struct v2f
            {
                float2 uv : TEXCOORD0;
				float3 worldNormal : TEXCOORD1;
				fixed3 worldPos : TEXCOORD2;
                float4 vertex : SV_POSITION;
            };

            sampler2D _MainTex;
            float4 _MainTex_ST;
			float _AlphaScale;

            v2f vert (appdata v)
            {
                v2f o;
                o.vertex = UnityObjectToClipPos(v.vertex);
				o.worldNormal = UnityObjectToWorldNormal(v.normal);
				o.worldPos = mul(unity_ObjectToWorld, v.vertex).xyz;
                o.uv = TRANSFORM_TEX(v.uv, _MainTex);
                return o;
            }

            fixed4 frag (v2f i) : SV_Target
            {
				fixed3 worldNormal = normalize(i.worldNormal);
				fixed3 worldLightDir = normalize(UnityWorldSpaceLightDir(i.worldPos));
				
                // sample the texture
                fixed3 col = tex2D(_MainTex, i.uv).rgb;
				
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;

				fixed3 diffuse = _LightColor0.rgb * col * max(0, dot(worldNormal, worldLightDir)) + ambient;
				

                return fixed4(diffuse,_AlphaScale);
            }
            ENDCG
        }
    }
}
~~~



#### 效果

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
>>>>>>> 50d25bf32498063c00489602285c7ebea2e3adef
