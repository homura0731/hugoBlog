---
title: "[鐵人賽Day5] SingalR 前端API運用"
date: 2018-09-24T13:45:05+08:00
draft: true
categories: [2019鐵人賽]
tags: [2019鐵人賽]
---
# 連接事件
建立連線，我們需要建立一個連線物件，withUrl裡的參數放入要連接的Hub名稱，再用build建立。
``` js
var connection = new signalR.HubConnectionBuilder().withUrl("/Hub名稱").build();
```
> 註：Hub名稱不分大小寫

## 開始連接
使用`connection.start()`方法開始連接Server，連接錯誤時可用`catch()`捕捉錯誤
``` js
connection.start().catch(function(err){
    // 錯誤處理
});
```
## 關閉連接
使用`connection.stop()`方法開始連接Server，一樣錯誤時可用`catch()`捕捉錯誤
``` js
connection.stop().catch(function(err){
    // 錯誤處理
});
```
`connection.onclose()`可以監聽連接關閉瞬間
``` js
connection.onclose(function(e){
    // 關閉時想做的事
});
```

# 其他事件
除了連接和關閉事件比較特別之外，其他事件都是自己自訂的事件，當然大部分的動作都能叫`SignalR API`不需要全部自己實作。

大致流程向這樣，首先使用`Connection.invoke()`呼叫`Hub`的方法，這時`Hub`會依照`SendAsync()`寫的對象傳回去給`connection.on()`接收，我們以`SendMessage`為例，JS像下面這樣寫

``` js
connection.invoke("SendMessage", user, message).catch(function (err) {
    return console.error(err.toString());
});
```
invoke的第一個變數會呼應到`Hub`的方法名稱，所以上面那樣會呼叫到下面這個`Hub`方法
``` cs
public async Task SendMessage(string user, string message)
{
    await Clients.All.SendAsync("ReceiveMessage", user, message);
}

```
然後`SendAsync()`第一個變數會丟回`conection.on()`事件裡。

``` js
connection.on("ReceiveMessage", function (user, message) {
    // 接收後要做的事
});
```

> 註：這3個方法除了第一個變數是對象外，其他都是能自訂的

## 後記
到這邊已經學會SignalR的最基礎了，下一篇我們來講講群組的傳遞。



