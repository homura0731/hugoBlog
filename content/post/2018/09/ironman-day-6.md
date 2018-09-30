---
title: "[鐵人賽Day6] SingalR 使用者及群組關係"
date: 2018-09-26T20:13:55+08:00
draft: true
categories: [2019鐵人賽]
tags: [2019鐵人賽]
---
# 前言
今天來介紹群組的運作，不知道會不會有人問為什麼要群組？？總不能每次訊息都傳給所有人吧!XD

所以今天就讓我們好好來介紹一下SignalR群組運作的方式。

# 使用方法
主要就使用2個方法，`Groups.AddToGroupAsync()`和`Clients.Group()`2種。
加入群組使用
``` cs
Groups.AddToGroupAsync(Context.ConnectionId, groupName);
```
`Context.ConnectionId`為`SignalR`產生的ID

`groupName`為要加入的群組

群組傳送訊息使用`Clients.Group()`
``` cs
Clients.Group(groupName).SendAsync("SendGroupMethod", "GroupMessage");
```
`groupName`是要傳送得群組
`SendAsync()`第一個參數為回傳要呼叫的前端方法，可任意修改名稱，第二個參數為回傳的訊息。

大概就這樣，感覺好像沒講什麼.....
那帶大家來實做一下吧!!

# 群組實作
我們用Day3得實作為基礎繼續做下去，如果沒做到的可以往回看Day3。

## 後端部分
首先我們在Hub新增方法`AddGroup()`，用來加入群組使用，內容大概像下面這樣

``` cs
public async Task AddGroup(string groupName, string username)
{
    await Groups.AddToGroupAsync(Context.ConnectionId, groupName);
    await Clients.Group(groupName).SendAsync("RecGroupMsg", $"{username} 已加入 群組：{groupName}。");
}
```
`AddToGroupAsync()`加入群組後，再回傳一個群組訊息告知大家誰加入了群組。

在建立一個群組傳送訊息的方法`SendMessageToGroup()`

``` cs
public Task SendMessageToGroup(string groupName, string username, string message)
{
    return Clients.Group(groupName).SendAsync("ReceiveMessageGroup", username, message);
}
```
跟上面的方法大同小異，只是多個訊息變數，還有傳回去時要多傳一個使用者，讓大家知道是誰傳的。

## 前端部分
首先我們要創建一個群組列表和加入群組按鈕，讓大家選擇要加入哪一個群組
``` html
<select id="group">
    <option>貓派</option>
    <option>狗派</option>
</select>
<button type="button" id="addGroupBtn">加入群組</button>
<button type="Button" id="submitGroupBtn">送出給群組</button><br>

```
建立加入群組的事件到`ButtonClick`裡，然後傳回給`AddGroup`
``` js
document.getElementById("addGroupBtn").addEventListener("click", function (event) {
    var user = document.getElementById("name").value;
    var group = document.getElementById("group").value;
    connection.invoke("AddGroup",group, user ).catch(function (err) {
        return console.error(err.toString());
    });
    event.preventDefault();
});
```
建立接收的事件，用來接收群組是否加入成功
``` js
connection.on("RecGroupMsg", function (message) {
    var msg = message;
    var li = document.createElement("li");
    li.textContent = msg;
    document.getElementById("msgDiv").appendChild(li);
});
```
再來建立群組訊息事件和接收事件
``` js
// 群組訊息Button事件
document.getElementById("submitGroupBtn").addEventListener("click", function (e) {
    var user = document.getElementById("name").value;
    var group = document.getElementById("group").value;
    var message = document.getElementById("msg").value;

    connection.invoke("SendMessageToGroup",group, user, message ).catch(function (err) {
        return console.error(err.toString());
    });
    event.preventDefault();
});

// 群組訊息接收事件
connection.on("ReceiveMessageGroup", function (groupName, user, message) {
    var msg = message;
    var encodedMsg = `[群組訊息(${groupName})]${user}：${msg}`;
    var li = document.createElement("li");
    li.textContent = encodedMsg;
    document.getElementById("msgDiv").appendChild(li);
});

```

這樣就大功告成了！
效果如下

<video width="528" controls>
    <source src="signalRGroup.mp4" type="video/mp4">
</video>

# 參考
- [https://docs.microsoft.com/zh-tw/aspnet/core/signalr/groups?view=aspnetcore-2.1](https://docs.microsoft.com/zh-tw/aspnet/core/signalr/groups?view=aspnetcore-2.1)
