---
title: "Lodash - 使用debounce做事件延遲"
date: 2018-09-05T20:54:53+08:00
categories: [JavaScript]
tags: [JavaScript, Lodash, debounce]
---
# 前言
這篇也是在IT邦幫忙解決的題目，最初問法是說做一個計時器，結果搞半天不是，
他是想做一個事件延遲3秒再執行才對，而且如果按鈕連續點擊，會以最後一次點擊完3秒後才執行事件。

# 解決方法
這個其實是我在學Vue.js的過程碰巧看到的函式庫Lodash，它的debounce方法可以延遲執行。

> 註：debounce可以翻防抖動或防反動都行

# 實作
## 引用Lodash
引入CDN
```html
<script src="https://cdn.jsdelivr.net/npm/lodash@4.17.10/lodash.min.js"></script>
```
NPM安裝
``` bash
$ npm i --save lodash
```
```js
var _ = require('lodash');
```
## 實作
先做出一個`Button`和一個訊息`DIV`
```html
<button id="showBtn" type="button">顯示訊息</button>
<div id="Message"></div>
```
寫一個函數顯示訊息，並使用`_.debounce()`延遲執行訊息消失事件
```js
var showBtn = document.getElementById('showBtn');
var msg = document.getElementById('Message');

// 消失事件
var hideMsg = function(){
    msg.innerHTML = '';
}

// 延遲一秒消失
var debounce = _.debounce(hideMsg, 1000);

// 點擊事件
showBtn.onclick = function(){
    msg.innerHTML = '點擊成功';
    debounce();
};
```
> 註：如果_.debounce()要放在funtion或變數裡，不然會沒效果

## Demo
點完一次一秒會消失，如果一直點的話不會消失，直到停止點擊一秒後才消失。

<button id="showBtn" type="button">顯示訊息</button>
<div id="Message"></div>
<script src="https://cdn.jsdelivr.net/npm/lodash@4.17.10/lodash.min.js"></script>

<script>
    var showBtn = document.getElementById('showBtn');
    var msg = document.getElementById('Message');
    var hideMsg = function(){
        msg.innerHTML = '';
    }
    
    var debounce = _.debounce(hideMsg, 2000);
    showBtn.onclick = function(){
        msg.innerHTML = '點擊成功';
        debounce();
    };
</script>

# 後記
其實這個還蠻好玩的，只是之前實作的時候是用Vue.js，沒想到直接實作卡那麼久，
原來是`_.debounce()`要包在function裡面不然他就不會跑了，就這樣鬼打牆好久，不過有學到東西是最重要的。

# 參考＆延伸閱讀
[官方文件](https://lodash.com/docs/4.17.10#debounce)

[lodash debounce實現](https://github.com/lishengzxc/bblog/issues/7)