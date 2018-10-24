---
title: "[鐵人賽Day10] SignalR 組態設定"
date: 2018-10-23T19:06:44+08:00
categories: [2019鐵人賽]
tags: [2019鐵人賽, SignalR, ASP.NET Core, JavaScript]
---
先來碎碎念一下，昨天`gitpage`一直有問題，害我`git push`好幾次，後來發現repo裡的文件是新的沒錯，今天一看又好了，真是的浪費我快30分鐘

今天來講講SignalR的組態設定，組態有分Server組態和用戶組態2種，就來看看怎麼設定吧！
# Server組態設定
主要在`Starup.cs`裡面設定，可以對全部Hub設定，也能只對一個Hub設定。

## 全部Hub設定
``` cs
services.AddSignalR(options =>
{
    options.HandshakeTimeout = TimeSpan.FromMinutes(3);
    options.KeepAliveInterval = TimeSpan.FromHours(1);
    options.EnableDetailedErrors = true;
});
```

## 單一Hub設定
``` cs
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.HandshakeTimeout = TimeSpan.FromMinutes(3);
    options.KeepAliveInterval = TimeSpan.FromHours(1);
    options.EnableDetailedErrors = true;
});
```
## 可設定變數

|名稱             |解釋       |
|----------------|-----------|
|HandshakeTimeout|初次建立連線的時間，超過時間User端都沒回應會建立連線失敗，其實這命名正是WebSocket的握手，時間使用`TimeSpan`設定|
|KeepAliveInterval|無傳送訊息持，會ping Server以保持連線的時間，一樣使用`TimeSapn`設定|
|EnableDetailedErrors|是否顯示錯誤詳細狀況，預設是false，建議只有開發時打開就好|

## 傳輸和記憶體緩衝相關設定
這邊文件寫得不太清楚，文件只說設定會傳到委派`MapHub`，後來實際做時發現MapHub原來有2個變數，一個就是傳入`HttpConnectionDispatcherOptions`的設定，設定方法一樣是`Starup.cs`裡面，大概像下面這樣
``` cs
routes.MapHub<ChatHub>("/chatHub", options =>
{
    options.WebSockets.CloseTimeout = TimeSpan.FromMilliseconds(500);
    options.ApplicationMaxBufferSize = 0;
    options.TransportMaxBufferSize = 0;
}
```
選項很多請看結尾的的官方文件

# 用戶端組態設定
在用戶端部分有分成`.NET`和`JS`2種，`.NET`版文件在MSDN文件是用`WPF`當範例，因為他不跨平台，所以這邊先不介紹。
## JS用戶端
設定方式是在建立後的`HubConnection`，在物件屬性中直接設定，例如像下面這段程式碼。
``` js
var connection = new signalR.HubConnectionBuilder().withUrl("/chatHub").build();

connection.serverTimeoutInMilliseconds = 500; // 設定500毫秒無回應，切斷連線
```
或是從一開始建立就設定好也行
``` js
var connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub", {
        serverTimeoutInMilliseconds: 500
    })
    .build();
```
可以設定的選項如下

|名稱             |解釋
|----------------|-----------|
|serverTimeoutInMilliseconds|連線無回應的時間限制|
|accessTokenFactory|驗證用的Bearer Token|
|skipNegotiation|跳過交涉的步驟，只支援WebSocket傳輸時|

> 註：.NET用戶端可以設定很多，不知為什麼JS用戶端只有3個...

今天大概這些啦，MSDN文件感覺很多寫的太簡單，而且又是機器翻譯，實在是看不太懂....

無聊的概念大概都講完了，明天就要開始實作吧！！

# 參考
- [MSDN文件](https://docs.microsoft.com/zh-tw/aspnet/core/signalr/configuration?view=aspnetcore-2.1)
