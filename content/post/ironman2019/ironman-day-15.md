---
title: "[鐵人賽Day15] 實作一個共用塗鴉牆(4)"
date: 2018-10-11T20:38:57+08:00
draft: true
categories: [2019鐵人賽]
tags: [2019鐵人賽]
---
今天應該最後一篇塗鴉牆了，來講講畫版載入時同步的功能，在載入時必須和前面畫過的畫面同步，避免之後進來的人看到的畫面跟先進來看到會有不一樣的狀況
，所以我們得做一個方法，在載入時就把存起來的動作一次畫完。

# 同步不同時間連線的塗鴉板
我們可以回想一下Day11的Service寫法，其實寫法一樣的，比耗工應該是前端部分，需要把一次把大量的動作畫完
## 建立DrawService.cs
這次比上次簡單，我們不需要移除的方法，只要增加就好，但是還要新增一個方法取得list。
``` cs
using System;
using System.Collections.Generic;
using DrawWall.Models;

namespace DrawWall.Service
{
    public class DrawService
    {
        private List<DrawJson> _list;
        public DrawService()
        {
            _list = new List<DrawJson>();
        }
        public void AddList(DrawJson drawJson)
        {
            _list.Add(drawJson);
        }
        public List<DrawJson> GetList(DrawJson drawJson)
        {
            return _list;
        }
    }
}
```
在`Starup.cs`引用和註冊
``` cs
using DrawWall.Service;
namespace DrawWall
{
    public class Startup
    {
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddSignalR();
            services.AddSingleton<DrawService>();
        }
    }
}
```
## 修改DrawHub
注入到Hub建構子內，然後我們新增一個方法`GetDraw`來取得所有的的繪圖過程，還有一個地方要注意，不要傳給所有人，只要傳給呼叫的使用者即可，`SendDraw`要把Json資料存進`drawService`中。
``` cs
using Microsoft.AspNetCore.SignalR;
using System.Threading.Tasks;
using DrawWall.Models;
using DrawWall.Service;
using System.Collections.Generic;

namespace DrawWall.Hubs
{
    public class DrawHub : Hub
    {
        public DrawService _drawService;
        public DrawHub(DrawService drawService)
        {
            _drawService = drawService;
        }
        public async Task SendDraw(DrawJson drawJson)
        {
            _drawService.AddList(drawJson);
            await Clients.All.SendAsync("ReceiveDraw", drawJson);
        }
        public async Task GetDraw()
        {
            await Clients.Caller.SendAsync("ReciveAllDraw", _drawService.GetList());
        }
        
    }
}
```
## 前端連線重繪
前端部分我們連接成功時，呼叫`GetDraw`方法，並把取得的資料全部重繪一遍
``` js
connection.start().then(function () {
    connection.invoke('GetDraw').catch(function (err) {
        console.log(err);
    });

}).catch(function (err) {
    return console.error(err.toString());
});

connection.on("ReciveAllDraw", function (json) {
    console.log(json);
    for (var i = 0; i < json.length; i++) {
        switch (jsonp[i].mode) {
            case 'line':
                ctx.strokeStyle = jsonp[i].color;
                ctx.lineWidth = jsonp[i].lineWidth;
                ctx.beginPath();
                ctx.moveTo(jsonp[i].startPos[0], jsonp[i].startPos[1]);
                ctx.lineTo(jsonp[i].endPos[0], jsonp[i].endPos[1])
                ctx.closePath();
                ctx.stroke();
                break;
            case 'eraser':
                var _eraserWidth = jsonp[i].eraserWidth;
                ctx.clearRect(jsonp[i].startPos[0] - (_eraserWidth / 2), jsonp[i].startPos[1] - (_eraserWidth / 2), _eraserWidth, _eraserWidth);
                break;
        }
    }
});

```
這樣就完成囉！
不過今天這篇有點短，不過文章開頭說最後一篇了，所以我們來聊聊我們還能做些什麼
# 塗鴉版未完成事項
- 把剩下Cavans的繪圖方法都做成工具
- 加入連線名單及畫圖時的使用者名稱


