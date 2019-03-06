## roguelike_forked_for_webgl

this is a fork from unity tutorial [2d-roguelike](https://unity3d.com/jp/learn/tutorials/projects/2d-roguelike-tutorial)

I add a ios webgl support using webaudio hack (see * 1) and lib(see * 2) and my hack(see * 3) to this unity game 

run the game by click [https://chooaya.github.io/roguelike_forked_for_webgl/index.html](https://chooaya.github.io/roguelike_forked_for_webgl/index.html) 

## Lib
- *1 [web-audio-on-ios](https://paulbakaus.com/tutorials/html5/web-audio-on-ios/)
- *2 [WebAudio](https://github.com/uupaa/WebAudio.js)

## My Hack

*3:

change index.html

before
```html


<!DOCTYPE html>
<html lang="en-us">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>Unity WebGL Player | Roguelikedemo</title>
    <link rel="shortcut icon" href="TemplateData/favicon.ico">
    <link rel="stylesheet" href="TemplateData/style.css">
    <script src="TemplateData/UnityProgress.js"></script>  
    <script src="Build/UnityLoader.js"></script>
    <script>
      var gameInstance = UnityLoader.instantiate("gameContainer", "Build/rougue_webgl_build_f.json", { onProgress: UnityProgress });
    </script>
  </head>
  <body>
    <div class="webgl-content">
      <div id="gameContainer" style="width: 960px; height: 600px"></div>
      <div class="footer">
        <div class="webgl-logo"></div>
        <div class="fullscreen" onclick="gameInstance.SetFullscreen(1)"></div>
        <div class="title">Roguelikedemo</div>
      </div>
    </div>
  </body>
</html>
```


to the after code
```html
<!DOCTYPE html>
<html lang="en-us">

<head>
  <meta charset="utf-8">
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
  <title>Unity WebGL Player | Roguelikedemo</title>
  <link rel="shortcut icon" href="TemplateData/favicon.ico">
  <link rel="stylesheet" href="TemplateData/style.css">
  <script src="TemplateData/UnityProgress.js"></script>
  <script src="Build/UnityLoader.js"></script>
</head>

<body>
  <div class="webgl-content">
    <div id="gameContainer" style="width: 960px; height: 600px"></div>
    <div class="footer">
      <div class="webgl-logo"></div>
      <div class="fullscreen" onclick="gameInstance.SetFullscreen(1)"></div>
      <div class="title">Roguelikedemo</div>
    </div>
  </div>
  <script src="lib/WebModule.js"></script>
  <script src="lib/WebAudio.js"></script>
  <script>
    var gameInstance = null;
    WebAudio.init(function (audioContext) { // @arg AudioContext|null
      if (WebAudio.ready) {
        window.WEBAUDIO = {
          audioInstances: [],
          audioContext: {},
          audioWebEnabled: 0
        };
        window.WEBAUDIO.audioContext = WebAudio.ctx;
        window.WEBAUDIO.audioWebEnabled = 1;
        gameInstance = UnityLoader.instantiate("gameContainer", "Build/rougue_webgl_build_f.json", { onProgress: UnityProgress });
      } else {
        alert("WebAudio not function");
      }
    }, document.body);
  </script>
</body>

</html>
```


 change UnityLoader.js(function:compatibilityCheck)
before
```js
  compatibilityCheck: function (gameInstance, onsuccess, onerror) {
    if (!UnityLoader.SystemInfo.hasWebGL) {
      gameInstance.popup("Your browser does not support WebGL",
        [{text: "OK", callback: onerror}]);
    } else if (UnityLoader.SystemInfo.mobile) {
      gameInstance.popup("Please note that Unity WebGL is not currently supported on mobiles. Press OK if you wish to continue anyway.",
        [{text: "OK", callback: onsuccess}]);
    } else if (["Firefox", "Chrome", "Safari"].indexOf(UnityLoader.SystemInfo.browser) == -1) {
      gameInstance.popup("Please note that your browser is not currently supported for this Unity WebGL content. Press OK if you wish to continue anyway.",
        [{text: "OK", callback: onsuccess}]);
    } else {
      onsuccess();
    }

  },
```

to the after code
```js
  compatibilityCheck: function (gameInstance, onsuccess, onerror) {
    onsuccess();
    //if (!UnityLoader.SystemInfo.hasWebGL) {
    //  gameInstance.popup("Your browser does not support WebGL",
    //    [{text: "OK", callback: onerror}]);
    //} else if (UnityLoader.SystemInfo.mobile) {
    //  gameInstance.popup("Please note that Unity WebGL is not currently supported on mobiles. Press OK if you wish to continue anyway.",
    //    [{text: "OK", callback: onsuccess}]);
    //} else if (["Firefox", "Chrome", "Safari"].indexOf(UnityLoader.SystemInfo.browser) == -1) {
    //  gameInstance.popup("Please note that your browser is not currently supported for this Unity WebGL content. Press OK if you wish to continue anyway.",
    //    [{text: "OK", callback: onsuccess}]);
    //} else {
    //  onsuccess();
    //}

  },
```


 change rougue_webgl_build_f.asm.framework.unityweb(function:_JS_Sound_Init)
before
```js
function _JS_Sound_Init() {
 try {
  window.AudioContext = window.AudioContext || window.webkitAudioContext;
  WEBAudio.audioContext = new AudioContext;
  WEBAudio.audioWebEnabled = 1;
 } catch (e) {
  alert("Web Audio API is not supported in this browser");
 }
}
```


to the after code
```js
function _JS_Sound_Init() {
 try {
     if (window.WEBAUDIO)
     {
         WEBAudio = window.WEBAUDIO;
     } else {
  window.AudioContext = window.AudioContext || window.webkitAudioContext;
  WEBAudio.audioContext = new AudioContext;
  WEBAudio.audioWebEnabled = 1;
     }
 } catch (e) {
  alert("Web Audio API is not supported in this browser");
 }
}
```
