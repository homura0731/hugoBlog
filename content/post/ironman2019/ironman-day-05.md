---
title: "[鐵人賽Day5] 使用SignalR Hub (2)"
date: 2018-10-18T20:38:52+08:00
categories: "2019鐵人賽"
tags: ["2019鐵人賽", "SignalR", "ASP.Net Core"]
---

今天來點輕鬆的吧！來寫寫強型別的Hub和把HubContext注入Controller

# 強型別Hub的好處
SignalR的`Hub`介面只規定我們要實作`OnConnectedAsync`和`OnDisconnectedAsync`兩個事件而已，其他的事件名稱全都是自定義的`magic string`，
這樣會可能會照成大小寫拼錯，或是拼錯了不知道，所以強行別Hub就能幫助我們在開發偵錯時就先找到錯誤。

## 實作強行別Hub
其實就是也就是定義一個介面`Interface`而已，我們來簡單設計一個介面，大概像下面這樣

``` cs
public interface IChatClient
{
    Task AddGroup(string groupName, string user);
    Task ReceiveMsgGroup(string groupName, string user, string message);
}
```
再來套用到自己寫的`Hub`上，使用繼承的方式加入，因為是它是一個泛型`Hub<T>`
``` cs
public class ChatHub : Hub<IChatClient>
{
    public async Task AddGroup(string user, string message)
    {
        await Groups.AddToGroupAsync(Context.ConnectionId, groupName);
        await Clients.Group(groupName).SendAsync("RecGroupMsg", $"{user} 已加入 群組：{groupName}。");
    }

    public Task ReceiveMsgGroup(tring groupName, string user, string message)
    {
        return Clients.Group(groupName).SendAsync("ReceiveMessageGroup", groupName , username, message);
    }
}
```

# HubContext注入Controller
這樣的好處是能把方法寫到Controller裡面，缺點則是我們呼叫不到`Context`物件，這樣就沒辦法知道是哪個`ClientID`傳過來的

接下來就來看看怎麼把`HubContext注入Controller`注入Controller吧！

## 建立一個Controller
我們用Day3同一個[專案](https://drive.google.com/file/d/1CSiqGkTjQvWL25iiYtUmMNrrL4m_JVrG/view)，建立一個Controller資料夾，在裡面建立一個`ChatController.cs`
``` cs
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.SignalR;
using CoreWeb.Hubs;

namespace CoreWeb.Controllers
{
    public class ChatController : Controller
    {

    }
}
```
建立一個變數，在建構子注入`HubContext<T>`，`<T>`為自己建立的任意`Hub`
``` cs
private readonly IHubContext<ChatHub> _hubContext;
public ChatController(IHubContext<ChatHub> hubContext)
{
    _hubContext = hubContext;
}
```
傳送訊息改成從Controller進入，所以我們要在Controller建立傳送訊息的方法，方法基本上跟在Hub裡面時一模一樣
``` cs
public async Task SendMessage(string user, string message)
{
    await _hubContext.Clients.All.SendAsync("ReceiveMessage",user, message);
}
```
在`Starup.cs`註冊`MVC`方法
``` cs
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR().AddMessagePackProtocol();
    services.AddMvc();
}
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseSignalR(routes =>
    {
        routes.MapHub<ChatHub>("/chatHub");
    });
    app.UseDefaultFiles();
    app.UseStaticFiles();
    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Chat}/{action=Index}/{id?}");
    });

}
```
前端的呼叫方法有點不一樣，得換用`ajax`的方式，這邊我們用HTML5內建的`fetch()`
``` js
document.getElementById("submitBtn").addEventListener("click", function (event) {
    var user = document.getElementById("name").value;
    var message = document.getElementById("msg").value;
    fetch(`Chat/SendMessage?user=${user}&message=${message}`,{
        method:"GET"
    })
    event.preventDefault();
});
```
前端接收則還是保持原本的狀況，大概這樣就完成了，下一篇我們在來看看前端的方用法。

# 參考
- [MSDN文件](https://docs.microsoft.com/zh-tw/aspnet/core/signalr/hubs?view=aspnetcore-2.1)