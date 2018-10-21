---
title: "[鐵人賽Day9] 使用MessagePack傳輸更小的資料"
date: 2018-09-30T14:55:04+08:00
draft: true
categories: [2019鐵人賽]
tags: [2019鐵人賽]
---

`MessagePack`是一個類似Json格式但是比Json速度更快、檔案更小，不過似乎還沒很流行就是了，既然`ASP.Net Core SignalR`文件上面有特別提到，那我們今天就來用用看這個新的資料格式吧!


# MessagePack跟Json差在哪?
MessagePack使用二進位序列化組成，下面是官方示意圖
![MeassagePackDEMO](MeassagePackDEMO.PNG)

他會幫你把資料轉成二進位已達成更小的體積，官網有個Demo可以幫你把JSON轉成MessagePack，點進去後再點上方得Try

[DEMO網址](https://msgpack.org/)

# 安裝及使用MessagePack
官網不知道為什麼同一語言有好幾個版本...，SingalR本身就支援MessagePack了，有自己的版本，所以就使用SignalR的版本吧!

## 後端部分
先用aspnet cli安裝
``` shell
dotnet add package Microsoft.AspNetCore.SignalR.Protocols.MessagePack
```
首先先在Starup.cs註冊服務，放在AddSignalR後面。
``` cs
services.AddSignalR().AddMessagePackProtocol();
```

## 前端部分
我們要先用npm安裝套件
``` shell
$ npm install @aspnet/signalr-protocol-msgpack
```

然後去`node_module`裡面找出下面2個檔案`signalr-prtocol-msgpack.js`和`msgpack5.js`，把它移動到`wwwroot/lib`底下，覺得這樣有點麻煩，所以我直接把他寫成shell指令了...

**cmd**
``` shell
copy node_modules\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js wwwroot\lib\signalr-protocol-msgpack.js
copy node_modules\msgpack5\dist\msgpack5.js  wwwroot\lib\msgpack5.js
```
**bash**
``` 
cp node_modules/@aspnet/signalr-protocol-msgpack/dist/browser/signalr-protocol-msgpack.js wwwroot/lib/signalr-protocol-msgpack.js
cp node_modules/msgpack5/dist/msgpack5.js  wwwroot/lib/msgpack5.js
```
開啟`wwwroot/index.html`引入js
``` html
<script src="lib/msgpack5.js"></script>
<script src="lib/signalr-protocol-msgpack.js"></script>
```
註冊到SignalR到protocol裡
``` js
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```


到這邊就OK了！你說好像看不出來有什麼變化??因為都是在SigalR內部跑的，其實可以用F12開發者工具來看

打開F12 -> NetWork -> 找到type是Websocket -> 選擇後右邊在選Frames 

來比較一下差別吧，資料統一貓派群組、名字和訊息都是123

下面這個是JSON，資料長度是傳送`91`，接收`73`

![Json](Json.gif)

這個是MessagePack，傳送`41`，接收`49`

![MessagePack](MessagePack.gif)

結論是其實MessagePack資料量真的小蠻多，今天大概就介紹這樣吧！

# 參考
- [MSDN文件](https://docs.microsoft.com/zh-tw/aspnet/core/signalr/messagepackhubprotocol?view=aspnetcore-2.1)