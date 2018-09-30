---
title: "[鐵人賽Day7] 使用Json傳遞訊息"
date: 2018-09-29T16:52:05+08:00
draft: true
categories: [2019鐵人賽]
tags: [2019鐵人賽]
---
# 前言
今天要來講講怎麼使用JSON傳遞訊息，

# 在Service註冊
其實這步是可以跳過，因為SignalR預設會開啟`AddJsonProtocol()`，如果要全部使用同一個預設的Json格式的，我查過答案要使用mvc才行，所以這又是另外的用法，我會擺在比較後面講。

# 實作
我們先建立一個Class來當Json的預設格式，先建立一個檔案`JsonFromat.cs`，程式碼大概像下面這樣。
``` cs
using System;

namespace CoreWeb.JsonFormat
{
    public class ReponseJson
    {
        public int id { get; set; }
        public string user { get; set; }
        public string message { get; set; }
        public string group { get; set; }
    }
}
```

再來我們去Hub裡面載入，首先using剛剛建立Class
``` cs
using CoreWeb.JsonFormat;
```
然後在Hub建立一個變數，在建構子裡面物件化
``` cs
public class ChatHub : Hub
{
    private ReponseJson responseJson;
    public ChatHub()
    {
        responseJson = new ReponseJson();
    }
}
```
這樣不管哪個方法都回應這個`responseJson`就好了，例如我們建立一個方法要傳給所有人大概會像下面這樣
``` cs
public async Task SendMessage(string user, string message)
{
    responseJson.user = user;
    responseJson.message = message;
    await Clients.All.SendAsync("ReceiveMessage", responseJson);
}
```
其實這個跟以前的Web API用法差不多....

然後User端在接收時，就會變成一個Json格式，沒填值的變數會自動補上Null，`Cosole.log()`出來大概就像下面這張圖。

![json格式](jsonformat.PNG)

# 後記
這篇好像有點少，寫完覺得有點慚愧哈哈，下一篇我們來講講新的訊息格式`MessagePack`吧!
