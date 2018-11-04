---
title: "[鐵人賽Day28] 實作Web即時共同編輯文件 (8) - 最後的BUG修正+小功能添加"
date: 2018-11-04T14:34:05+08:00
draft: true
categories: [2019鐵人賽]
tags: [2019鐵人賽]
---
真的是越複雜的功能，越容易出現不少的BUG，前面做了7篇原本以為我大概的狀況我都有抓到，直到昨天做完發現蠻多BUG，所以今天就來幫這個時做來做最後修正吧！

# 不同文件間上線名單互相干擾BUG
這個還真的是做完才發現，首先我們要到`FileHub`取得文件這邊，增加一個`fileName`參數，傳送回去`ReceiveUserList`也要多個`fileName`，這樣Client端才會知道這是哪個文件的UserList，再決定要不要更新使用者清單。
``` cs
public async Task GetFile(string fileName, string name)
{
    // 取得文件
    var file = _service.GetFile(fileName);
    // 加入使用者
    _service.AddUser(fileName, Context.ConnectionId, name);

    await Clients.Caller.SendAsync("ReceiveFile", file);
    await Clients.All.SendAsync("ReceiveUserList", fileName, _service.GetUserList(fileName));
}
```
再來是`EditText`這個方法，我原本就有設計要修改哪個文件，但是卻忘記丟回是哪個文件倍修改，記得回傳多補上一個`fileName`參數。
``` cs
// 編輯文件
public async Task EditText(string fileName, string cellName, string text)
{
    var editText = _service.EditFileCell(fileName, cellName, text, Context.ConnectionId);
    await Clients.All.SendAsync("ReceiveEditText", fileName, cellName, editText.text, editText.editor);
}
```
前端有3個接收事件要辨識是否為自己編輯的文件，分別是`ReceiveEditText`、`ReceiveUserList`、`ReceiveCancelEditText`，還有`ReceiveEditText`、`ReceiveUserList`記得加上`fileName`參數。
``` js
connection.on("ReceiveEditText", function (fileName, cellName, text, user) {
    // 判斷是否為同一個文件
    if (LoadFile === fileName) {
        var cell = document.getElementById(cellName);
        // 判斷是否為自己
        if (getQueryStringByName('id') != user) {
            cell.childNodes[0].value = text;
            cell.childNodes[0].style.backgroundColor = document.getElementById(user).childNodes[0].style.backgroundColor;
        }
    }
});

// 傳送使用者清單
connection.on("ReceiveUserList", function (fileName, userListData) {
    // 判斷是否為同一個文件
    if (LoadFile === fileName) {
        // 略...
    }
});

connection.on("ReceiveCancelEditText", function (fileName, cellName) {
    // 判斷是否為同一個文件
    if (LoadFile === fileName) {
        var cell = document.getElementById(cellName);
        cell.childNodes[0].style.background = 'transparent';
    }
});
```
# 添加編輯者提示

# 禁止2人同時編輯同個cell
