---
layout: page
title: Unity3D开发小游戏
titlebar: Game_Tetris
menu: Game_Tetris
subtitle:  <span class="mega-octicon octicon-person"></span>&nbsp;&nbsp; 恬静的小魔龙，程序猿一枚
css: ['about.css', 'sidebar-popular-repo.css', '../../bower_components/flag-icon-css/css/flag-icon.min.css']
permalink: /Game_Tetris
---



<html lang="en-us">

  <head>
    <meta charset="utf-8">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>Unity3D 俄罗斯方块游戏</title>
    <link rel="shortcut icon" href="/assets/Game/Game_Tetris/TemplateData/favicon.ico">
    <link rel="stylesheet" href="/assets/Game/Game_Tetris/TemplateData/style.css">
    <script src="/assets/Game/Game_Tetris/TemplateData/UnityProgress.js"></script>  
    <script src="/assets/Game/Game_Tetris/Build/UnityLoader.js"></script>
    <script>
      var gameInstance = UnityLoader.instantiate("gameContainer", "/assets/Game/Game_Tetris/Build/Game_Tetris.json", {onProgress: UnityProgress});
        function changeSceneName(){
         unityInstance.SendMessage("SceneManager","setSceneName","RimLight_Lambert");
      }
    </script>
  </head>
  <body>
    <div class="webgl-content">
      <div id="gameContainer" style="width: 960px; height: 600px"></div>
      <div class="footer">
        <div class="webgl-logo"></div>
        <div class="fullscreen" onclick="gameInstance.SetFullscreen(1)"></div>
        <div class="title">全屏</div>
      </div>
    </div>


  </body>
</html>
