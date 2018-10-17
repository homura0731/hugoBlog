---
title: "[鐵人賽Day4] 使用SignalR Hub(1)"
date: 2018-10-17T19:11:15+08:00
categories: [2019鐵人賽]
tags: [2019鐵人賽, SignalR, ASP.Net Core]
---
# SignalR Hub是什麼?
其實你從它的名字也看得出來是什麼，Hub不就是集線器的意思XD，不過這邊的線不是網路線而是`Browser`和`Server`溝通的橋樑，簡單來說就是幫你跟瀏覽器溝通的一個組件，有了他我們就能輕鬆撰寫溝通的事件，不用從最簡單連接關閉開始寫。

# Hub Interface
來看看`Hub`裡面它幫我實作了什麼

``` cs
public abstract class Hub : IDisposable
{
    protected Hub();

    public IHubCallerClients Clients { get; set; }

    public HubCallerContext Context { get; set; }

    public IGroupManager Groups { get; set; }

    public void Dispose();

    public virtual Task OnConnectedAsync();

    public virtual Task OnDisconnectedAsync(Exception exception);

    protected virtual void Dispose(bool disposing);
}

```

`Clients`基本上就是跟Client溝通用的物件，溝通的方法有3個

- `All` ：所有人
- `Group` ：群組
- `Caller`：單一對象

`Context`則是`Browser`傳送過來的資訊，這些資訊是由前端的SignalR產生的，內容如下


- `ConnectionId`：連接中的用戶識別碼
- `UserIdentifier`：連接識別碼
- `ConnectionId`：連接識別碼

`Groups`則是負責管理群組的屬性


`OnConnectedAsync`和`OnDisconnectedAsync`不用說就是連接和斷線的事件囉

# 使用方法
## 實作一個Hub類別
在上一篇實作中有做過，只要開一個類別繼承`Hub`，裡面只要用`override`實作`OnConnectedAsync`和`OnDisconnectedAsync`2個方法就行，其他的方法都可以自訂，回傳如果不是傳個單一用戶，建議都使用await 非同步的方式。
``` cs
public class ChatHub : Hub
{
    public async Task SendMessage(string user, string message)
    {
        await Clients.All.SendAsync("ReceiveMessage", user,message);
    }

    public Task SendMessageToCaller(string message)
    {
        await Clients.Caller.SendAsync("ReceiveMessage", message);
    }

    public Task SendMessageToGroups(string message)
    {
        List<string> groups = new List<string>() { "SignalR Users" };
        await Clients.Groups(groups).SendAsync("ReceiveMessage", message);
    }
    // 連線
    public override async Task OnConnectedAsync()
    {
        await base.OnConnectedAsync();
    }
    // 斷線
    public override async Task OnDisconnectedAsync(Exception exception)
    {
        await base.OnDisconnectedAsync(exception);
    }
}

```

## Configure中註冊建立的Hub
在`Starup.cs`的`Configure`中註冊，使用UseSignalR的Router裡註冊，可以註冊多個沒有限制

``` cs
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/chatHub");
});
```

Hub概念大概就這些，下一篇來講講Hub的延伸用法。

