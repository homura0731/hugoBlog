---
title: "Node.js - IT邦新文章通知小工具"
date: 2018-08-16T22:43:43+08:00
draft: true
categories: "Node.js"
tags: ["Node.js"]
---
# 前言
最近覺得看IT邦的新問題，每次都要一直F5覺得很麻煩，去年的鐵人賽有的很好玩的爬蟲主題[爬蟲始終來自惰性](https://ithelp.ithome.com.tw/users/20107159/ironman/1325)
，覺得很有趣所以想做一個實作一個推播用小工具。

# 實作環境
- node.js
- Mac OS

# 實作過程
## 建立需要的檔案
先開個資料夾，然後新增一個檔案叫index.js，之後程式碼都寫在裡面。
## 安裝需要的套件
安裝request和cheerio
``` bash
$ npm install request 
$ npm install cheerio
```
## 開始爬IT邦

``` js
const request = require('request');
var url = 'https://ithelp.ithome.com.tw';

request(url, function (error, response, body) {
    console.log('body:', body); 
})
```
這樣基本的網頁就抓到了

## 分析IT邦的DOM架構


