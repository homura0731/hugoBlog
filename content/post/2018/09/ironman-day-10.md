---
title: "[鐵人賽Day10] 使用強型別SignalR Hub"
date: 2018-10-02T23:19:52+08:00
categories: [2019鐵人賽]
tags: [2019鐵人賽]
---
今天來點輕鬆的吧！來寫寫強行別的Hub

# 為什麼要使用強型別Hub？
SignalR的`Hub`介面只規定我們要實作`OnConnectedAsync`和`OnDisconnectedAsync`兩個事件而已，其他的事件全都是自定義的`magic string`，
這樣會可能會照成大小寫拼錯，或是拼錯了不知道，也能幫助我們在開發偵錯時就先找到錯誤。

# 實作強行別Hub
其實就是也就是定義一個介面而已，我們來簡單設計一個介面，大概像下面這樣

``` cs
public interface IChatClient
{
    Task AddGroup(string groupName, string user);
    Task ReceiveMsgGroup(string groupName, string user, string message);
}
```
套用到自己寫的Hub上
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

這樣就完成了，感覺這篇也好少!XD