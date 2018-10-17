---
title: "[鐵人賽Day1] ASP.Net Core SignalR 導讀"
date: 2018-10-14T19:39:50+08:00
categories: [2019鐵人賽]
tags: [2019鐵人賽, SignalR]
---
今天第一天來點輕鬆的，我們來說說SignalR是什麼?

# ASP.NET Core SignalR是什麼？
SignalR是一個能輕鬆架構即時互動(real-time)Web App的Library，微軟在2013年推出這套Library，相信應該很多人玩過，但我這次要介紹的是ASP.NET Core 的版本，今年大概6月才正式第一版，所以算是很新的Library。

# 為什麼要選ASP.NET Core版本？
- 比較新還沒有什麼人分享
- .NET Core的跨平台特性，這樣不管在哪個平台都能實作，後來新增的WPF是例外，因為只支援Windows
- ASP.NET Core框架彈性比起ASP.NET高很多

# 使用SignalR的好處
SignalR有分前端和後端，前端提高的大量的方法簡化的WebSocket的用法，相信如果有在玩Websocket的應該知道有不少Websocket的前端框架出現，例如`Socket.io`
當然這邊想要自己用Websoket寫也是可以，後端則是`SigalR Hub API`幫忙控制Server和User之間的通訊和連接的控管，而且內建大量的控制方法，我們不需要自己再從頭撰寫。

# 什麼是Real-time Web App
一般的網頁如果想做聊天室，會寫個ajax的程式定時去跟Server確定有無新資料，這個動作叫作輪詢，但是時間過太久Server端的記憶體會釋放掉，導致你你要資料時，程式要整個重啟，
這時候就出現長輪詢，差別在於他會短時間送訊息給Server，讓Server保持執行的狀態，就不會因為太久沒連線而導致釋放掉。

但是這些方法都不是很好，所以在HTML5時出現新API `WebSocket`，可以達成真正的跟Server保持連線，一有用戶傳送訊息到Server，Server馬上會再把訊息送給所有的連接用戶。

流程大概像下面這幫圖

![SignalRHub](SignalRHub.PNG)



# 鐵人賽的文章方向
主要會講SignalR比較重要的概念，其他太細的可能不會提，例如ASP.NET Core或是一些前端的概念，其實SignalR概念也不多就是了XD，再來會帶大家寫一些好玩的實作，目前大概預定好了幾個，有`塗鴉板`、`投票系統`、`遠端操作系統`、`共用文件編輯`，大概這些，今天就輕鬆些先這樣囉!