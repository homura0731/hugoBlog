---
title: "SingalR 前端API運用"
date: 2018-09-24T13:45:05+08:00
draft: true
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

# 傳送訊息事件




