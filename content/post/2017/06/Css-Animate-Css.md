---
title: CSS - Animate.css 好用的CSS動畫函式庫
date: 2017-06-01 21:37:20
categories: "CSS"
tags: ["CSS","JavaScript","Animate.css"]
---
# 前言
今天上班時發現好玩的Css動畫函式庫 Animate.css，由Daniel Eden製作，Soure Code也有公開是GitHub上面，回家後馬上抓來玩玩看效果還不錯，在這邊紀錄一下筆記。

# 使用方法
先去[官網](https://daneden.github.io/animate.css/)取的Animate.css這隻檔案
然後引入自己的網頁裡
``` html
<link href="animate.css" rel="stylesheet" type="text/css">
```
再來只要修改標籤的class改成要的效果，效果可以看官網的範例，然後再class還要再加上animated就行了
例如我要給一行字執行wobble這個特效
``` html
<p class="wobble animated">Test</p>
```
這樣就OK了，不過只會執行一次喔!想要多次當然只能依靠JS幫忙
簡單寫一下，例如我要按下一個Button讓文字產生效果
Html部分
``` html
<button id="testBtn">Test</buttion>
<p id="testWord">Test Word</p>
```
JS部分
``` javascript
// js
var btn = document.getElementById('testBtn');
var animate = function(){
    var imageCss = docunment.getElementById('testWord');
    imageCss.className = "wobble animated"; 
}
btn.onclick = animated;

// jquery
$('#testBtn').click(function(){
    $('#testWord').attr("class","wobble animated");
});
```

# 簡單範例
因為同事問過是不是能用在圖片，所以我就用圖片當範例不用文字了!

<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script>
<link rel="stylesheet" type="text/css" href="animate.css" >

效果：
<select id="selAnime">
    <option value="wobble">wobble</option>
    <option value="fadeInRight">fadeInRight</option>
    <option value="jello">jello</option>
    <option value="hinge">hinge</option>
</select>
<button id="test">執行動畫</button>
<br><br>
<div id="animateTest">
<img src="0001536375.JPG" />
</div>
<script>
$('#test').click(function(){
    $('#animateTest > a > img').attr("class",$('#selAnime').val() + " animated" ).delay(500);
    setTimeout(animateCacel, 1000);
});
function animateCacel()
{
    $('#animateTest > a > img').attr("class", "");
}
</script>

# 後記
這個真的蠻好玩的，配合JS都能自己做出簡單的輪播，對後端工程師來說真的很好上手。

# 附錄
- [Animate.css的GitHub](https://github.com/daneden/animate.css)