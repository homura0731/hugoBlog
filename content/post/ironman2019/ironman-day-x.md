---
title: "[鐵人賽DayX]SignalR 安全性驗證"
date: 2018-10-20T15:08:02+08:00
draft: true
tags: [2019鐵人賽]
---
其實SignalR也提供了一些安全性的認證，不過我們並不是要做一個會員登入的機制，只講最基礎的驗證的方法，剩下會員機制該怎麼去實現，這邊應該讓大家自己去思考，總之今天就來講講身分認證和跨網域的方法。

# 身分認證的方式
最簡單的方法大家應該都是知道可以使用Cookie和JWT的方式，但是要怎麼使用SignalR傳遞呢？這就是今天要講的東西

## 使用Cookie認證

