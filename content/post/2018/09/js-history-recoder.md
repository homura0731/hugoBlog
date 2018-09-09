---
title: "JS - 步驟紀錄器實作"
date: 2018-09-08T21:12:17+08:00
categories: [JavaScript]
tags: [JavaScript, history recoder, debounce]
---
# 前言
昨天有人問了一題有趣的題目，內容是問要怎麼實作編輯器得上一步下一步動作，也是就是說要做一個步驟紀錄器，覺得沒做過這種東西覺得好像蠻有趣的，
就來試試看。

# 步驟紀錄器原理
## 基本原理
原理很簡單，先建立一個`Array`當作容器，每次輸入時使用就把資料塞進`Array`中，在使用一個變數紀錄當前的步驟，上一步就是把資料還原上一個`Array元素`的內容而已，是不是很簡單呢？XD
## 紀錄的頻率
HTML的DOM事件只能監控你每次輸入的瞬間，假如說你輸入`123`，這樣會產生3步，這樣感覺紀錄的太細膩，所以我們要做一個延遲，需要輸入後停止多久的時間，紀錄器才會把資料記錄下來。
## 紀錄器的空間控管
當然紀錄器的空間要有限制，不是一直`Array.push()`就好，這樣你會害使用者記憶體爆掉的....

所以我們一定要限制`Array`的大小，對使用者來說就是可以紀錄的幾個步驟。

# 步驟紀錄器實作
## 建立基本容器與變數
實作使用`TextArea`來當示範，首先先建立2個`Button`和`TextArray`
``` html
<button id="prev" type="button">上一步</button>
<button id="next" type="button">下一步</button>
<textarea id="text" cols="5"></textarea>
```
建立`Array`和使用的變數
``` js
var step = 0; // 步驟變數
var textList = new Array(); // array容器
var prev = document.getElementById('prev');
var next = document.getElementById('next');
var text = document.getElementById('text');
```
## 步驟事件建立及加入輸入的監聽
寫一個事件，每個步驟都會把`TextArea`的內容寫入`textList`，並寫步驟+1
``` js
function textWrite(){
    step++; 
    textList.push(text.value);
}
```
當然只有事件是不會自動執行的，此時我們需要監控`TextArea`的輸入，並執行上面的事件
``` js
text.addEventListener('input', textWrite);
```
## 上下步事件建立
這邊開始就簡單了，上一步只要讀取上個`Array`元素的資料並塞進`TextArea`，下一步則是倒過來。
``` js
prev.onClick = function(){
    // 判斷是否還有上一步
    if(step < 2){
        alert('沒有上一步');
    }else{
        step--;// 步驟往回一步
        text.value = textList[step - 1]; // 因為Array從0開始所以要-1
    }
}

next.onClick = function(){
    if(step => textList.length){
        alert('沒有下一步');
    }else{
        step++;
        text.value = textList[step - 1];
    }
}
```
## Demo
<button id="prev" type="button">上一步</button>
<button id="next" type="button">下一步</button>
<br><br>
<textarea id="text" cols="5" style="width:300px"></textarea>

<script>
var step = 0; // 步驟變數
var textList = new Array(); // array容器
var prev = document.getElementById('prev');
var next = document.getElementById('next');
var text = document.getElementById('text');
function textWrite(){
    step++; 
    textList.push(text.value);
}
text.addEventListener('input', textWrite);
prev.onclick = function(){
    if(step < 2){
        alert('沒有上一步');
    }else{
        step--;
        text.value = textList[step - 1];
    }
}
next.onclick = function(){
    if(step > textList.length - 1){
        alert('沒有下一步');
    }else{
        step++;
        text.value = textList[step - 1];
    }
}
</script>

## 延遲紀錄的實現
這個就要使用我的[上一篇文章](../lodash-debounce/)，使用Lodash的`_.debounce`就能實現了。
``` js
var debounce = _.debounce(textWrite, 500);// 延遲500毫秒紀錄
text.addEventListener('input', debounce);// 改成監聽延遲函數
```
## Demo2
<script src="https://cdn.jsdelivr.net/npm/lodash@4.17.10/lodash.min.js"></script>
<button id="prev2" type="button">上一步</button>
<button id="next2" type="button">下一步</button>
<br><br>
<textarea id="text2" cols="5" style="width:300px"></textarea>

<script>
var step2 = 0; // 步驟變數
var textList2 = new Array(); // array容器
var prev2 = document.getElementById('prev2');
var next2 = document.getElementById('next2');
var text2 = document.getElementById('text2');
function textWrite2(){
    step2++; 
    textList2.push(text2.value);
}
var debounce = _.debounce(textWrite2, 500);// 延遲500毫秒紀錄
text2.addEventListener('input', debounce);// 改成監聽延遲函數

prev2.onclick = function(){
    if(step2 < 2){
        alert('沒有上一步');
    }else{
        step2--;
        text2.value = textList2[step2 - 1];
    }
}
next2.onclick = function(){
    if(step2 > textList2.length - 1){
        alert('沒有下一步');
    }else{
        step2++;
        text2.value = textList2[step2 - 1];
    }
}
</script>

## 步驟器的限制
要限制很簡單，只要儲存容器本身要限制寬度就行，這邊主要的另一個問題應該是超出範圍是該怎麼辦？
答案很簡單就是把現有資料向前移動就行，然後在最後一項資料再塞進新資料。
``` js
var textList = new Array(3); // 限制Array只能4個元素
function textWrite(){
    // 判斷是否已到最大紀錄步驟
    if(step === textList.length){
        for(var i = 0;i < textList.length - 1; i++){
            textList[i] = textList[i + 1];
        }
        textList[step - 1] = text.value; 
    } else {
        step++; 
        textList[step] = text.value;
    }
}

```
## Demo3
<button id="prev3" type="button">上一步</button>
<button id="next3" type="button">下一步</button>
<br><br>
<textarea id="text3" cols="5" style="width:300px"></textarea>

<script>
var step3 = 0; // 步驟變數
var textList3 = new Array(3); // array容器
var prev3 = document.getElementById('prev3');
var next3 = document.getElementById('next3');
var text3 = document.getElementById('text3');
function textWrite3(){
    // 判斷是否已到最大紀錄步驟
    if(step3 === textList3.length){
        var temp;
        // 資料向前移動
        for(var i = 0;i < textList3.length - 1; i++){
            textList3[i] = textList3[i + 1];
        }
        textList3[step3 - 1] = text3.value; 
    } else {
        textList3[step3] = text3.value;
        step3++; 
    }
}
text3.addEventListener('input', textWrite3);
prev3.onclick = function(){
    if(step3 < 2){
        alert('沒有上一步');
    }else{
        step3--;
        text3.value = textList3[step3 - 1];
    }
}
next3.onclick = function(){
    if(step3 > textList3.length - 1){
        alert('沒有下一步');
    }else{
        step3++;
        text3.value = textList3[step3 - 1];
    }
}
</script>

# 後記
整體來說不難，最後一項`步驟器的限制`難度有比較高，我在想資料位移時還一直想到腦筋打結，原本是`for`遞減，後來想一想好像遞增比較好寫，
害我卡了很久，不過很多IT邦很多大大說編輯器很難寫是真的，雖然基本的觀念不是很難，但是細部的微調真的很難寫，例如我其時還少寫了一個步驟不在最後一步時，
要覆寫後面的步驟，實作起來應該是更難，我這邊就不做了(暈)。

