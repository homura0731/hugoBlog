---
title: "[2019鐵人賽]Day1 ASP.Net SignalR 導讀"
date: 2018-09-12T20:39:50+08:00
draft: true
---
# ASP.NET Core SignalR是什麼？
SignalR是一個能輕鬆架構互動Web App的Library，微軟在2013年推出這套Library，相信應該很多人玩過，但我這次要介紹的是ASP.NET Core 的版本，今年大概6月才正式第一版，所以算是很新的Library。

# 為什麼要選ASP.NET Core版本？
- 比較新還沒有什麼人分享
- .NET Core的跨平台特性，這樣不管在哪個平台都能實作
- ASP.NET Core的框架彈性很高

# 使用SignalR的好處
SignalR有分前端和後端，前端提高的大量的方法簡化的WebSocket的用法，相信如果有在玩Websocket的應該知道有不少Websocket的前端框架出現，
當然這邊想要自己用Websoket寫也是可以，後端則是SingalR Hub API幫忙控制Server和User之間的通訊和連接的控管，而且內建大量的控制方法，我們不需要自己再從頭撰寫。