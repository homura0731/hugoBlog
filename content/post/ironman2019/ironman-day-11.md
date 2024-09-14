---
title: "[鐵人賽Day11] 實作一個連線名單"
date: 2018-10-24T19:30:46+08:00
categories: "2019鐵人賽"
tags: ["2019鐵人賽", "SignalR", "ASP.NET Core", "JavaScript"]
---

今天要來個小型的實作一個連線名單，連線名單可以讓你知道目前在線的人有誰(廢話)，之前的範例都是在進入時傳送訊息給所有人，如果是中途進來的人，是無從得知目前有哪些人在線上
，如果要做一個聊天室沒這基本的功能也太爛了吧!XD

一樣使用Day3的範例來做，沒有的點[這裡](https://drive.google.com/file/d/1CSiqGkTjQvWL25iiYtUmMNrrL4m_JVrG/view)

# 製作一個名單容器和容器Service
我們先要製作一個容器，把目前上線的人資訊都存下來，至於要存哪些呢?我覺得`使用者名稱`和`連接ID`是一定需要的，再來就是上線的時間點，大概就這些，先建立一個`ConUserModel.cs`，
並建立一個Class當容器。
``` cs
using System;
using System.Collections.Generic;

namespace CoreWeb.Models
{
    public class ConUserModel
    {
        public string connectID { get; set; }
        public string username { get; set; }
        public DateTime onlineTime { get; set; }
    }
}
```
再來建立`ConUserService`，這邊我建立在同一個檔案內，在內部建立一個`List`泛型內部裝剛剛建立的`ConUserModel`，建立新增及移除List的方法
``` cs
public class ConUserService
{
    private List<ConUserModel> _list;
    public ConUserService()
    {
        _list = new List<ConUserModel>();
    }
    public List<ConUserModel> AddList(ConUserModel user)
    {
        _list.Add(user);
        return _list;
    }
    public List<ConUserModel> RemoveList(string connectID)
    {
        for (int i = 0; i < _list.Count; i++)
        {
            if (_list[i].connectID == connectID)
                _list.Remove(_list[i]);
        }
        return _list;
    }
}
```
再到`Starp.cs`註冊Service
``` cs
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ConUserService>();
    services.AddSignalR();
}
```

再來到我們建立一個Hub裡面引用`Core.Models`
``` cs
using CoreWeb.Models;
```
Hub內宣告`List<ConUserMode>`，建構子裡面接收從Servie傳過來的`List<ConUserMode>`，在載入到Hub內的List變數
``` cs
namespace CoreWeb.Hubs
{
    public class ChatHub : Hub
    {
        private ConUserService conUserList;
        public ChatHub(ConUserService conUserService)
        {
            conUserList = conUserService;
        }
    }
}
```
# 後端連線斷線的處理
後端每當有人連線或斷線時，都要重新傳送一份名單給所有使用者，所以得要在`OnConnectedAsync`和`OnDisconnectedAsync`處理，但是`OnConnectedAsync`裡面不能接參數，所以我們改用
連線後，再加入的群組的方式，首先寫加入群組的方法，群組假設叫`TestGroup`
``` cs
public async void AddConUserList(string user)
{
    var _user = new ConUserModel();
    _user.username = user;
    _user.connectID = Context.ConnectionId;
    _user.onlineTime = DateTime.Now;

    await Groups.AddToGroupAsync(Context.ConnectionId, "TestGroup");
    await Clients.Group("TestGroup").SendAsync("GetConList", conUserList.AddList(_user));
}
```
程式碼大概就是建立一個`ConUserModel`相關資料塞進去，加入群組後再加入上線名單。
然後切斷連線時，要通知群組所有人更新連線名單
``` cs
public override async Task OnDisconnectedAsync(Exception exception)
{
    await base.OnDisconnectedAsync(exception);
    await Clients.Group("TestGroup").SendAsync("GetConList", conUserList.RemoveList(Context.ConnectionId));
}
```

# 前端表現處理
我們先建立一個連線名單的`select html element`，記得加入`multiple`屬性讓它展開
``` html
<select id="ConList" multiple></select>
```
在連線的方法成功之後加入群組
``` js
document.getElementById('conOpenBtn').onclick = function () {
    connection.start().then(function() {
        console.log('start');

        var user = document.getElementById("name").value;
        // 加入群組
        connection.invoke("AddConUserList", user).catch(function (err) {
            return console.error(err.toString());
        });

    }).catch(function (err) {
        return console.error(err.toString());
    });
}
```
更新名單的方法每次都要清空List，再重新填入Server傳回來的名單
``` js
connection.on("GetConList", function (ConList) {
    console.log(ConList);
    _ConList.innerHTML = '';

    for (var i = 0; i < ConList.length; i++) {
        var user = document.createElement("option");
        user.text = ConList[i].username;
        _ConList.appendChild(user);
    }
})
```

# DEMO
<iframe width="560" height="315" src="https://www.youtube.com/embed/I0ZC48M9CDQ" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

今天大概就這些，雖然它只是一個小組件，但是之後的實作都能套用到它，明天會會來個比較難一點的。

# 範例下載
- [今天範例](https://drive.google.com/file/d/1CNBCcMhbJuhb8vtmxjBt1Z11iRwfaGKF/view?usp=sharing)
