---
title: "[鐵人賽Day19] 實作一個即時投票系統(4)"
date: 2018-10-18T21:38:53+08:00
draft: true
tags: [2019鐵人賽]
---

今天我們要來做投票列表，概念跟[Day11](/post/ironman2019/ironman-day-11)的上線名單一模一樣，忘記的可以去回顧一下，那麼我們就開始吧！

# 建立列表HTML元素
我們先把`voteMenu`區塊設成顯示，改成voteFrom區塊隱藏
``` html
<div id="voteRoom" style="display:none">

</div>
<!-- 所有投票選單 -->
<div id="voteMenu">

</div>
<!-- 新增投票區 -->
<div id="voteForm" style="display:none">

</div>
```
建立一個select 元素當作列表，記得加入`multiple`讓它展開，寬和高有訂一下，再來是2顆Button，一顆進入建立投票的頁面，一顆進入選擇的投票房間頁面。
``` html
<button id="creatVote" type="button">建立投票</button>
<br>
<select id="selVote" multiple style="height:200px;width:100px">
</select>
<br>
<button id="goVote" type="button">進入投票</button>
```
建立頁面切換事件
``` js
var creatVote = document.getElementById('creatVote');
var goVote = document.getElementById('goVote');
```

# 建立後端取得投票資料方法
因為資料是存在Servie裡面的List，所以先到`VoteService.cs`建立取得的方法
``` cs
public List<VoteModel> GetVoteList()
{
    return voteModelList;
}
```
再來到Hub建立取得方法，注意這邊取得時只要回傳給要求的用戶就好，不要全部都傳，不然會造成一個新的使用著上線想取得所有投票方法，每個人都全部丟一次資料，但是資料並無更新，所以這邊沒有這種必要。
``` cs
public async Task GetVoteList()
{
    await Clients.Caller.SendAsync("ReceiveVote", _voteService.GetVoteList());
}
```

# 當連線成功時取得資料
連線時取得資料，當然要寫在連接成功後的事件，也就是`connection.start().then()`裡面，其實day17已經寫過了，但是那天寫的是只取得該筆投票，這次是全部投票所以要改用上面新寫的方法。
``` js
connection.start()
.then(function () {
    connection.invoke("GetVoteList").catch(function (err) {
        return console.error(err.toString());
    });
})
.catch(function (err) {
    return console.error(err.toString());
});
```
取得成功後先清空頭票選單，再binding到我們建立的`select`元素中，完成後返回投票選單，返回投票選單這邊是因為跟建立投票一樣的事件，如果你在建立投票表單的話則會切換回頭票選單。
``` js
connection.on("ReceiveVoteList", function (json) {
    console.log(json);

    selVote.innerHTML = '';
    for (var i = 0; i < json.length; i++) {
        var sel = document.createElement('option');
        sel.innerHTML = json[i].voteName;
        sel.value = json[i].voteName;
        selVote.appendChild(sel);
    }
    // 如果在建立投票頁面，則切換回投票選單
    if (voteMenu.style.display === 'none'){
        voteForm.style.display = 'none';
        voteMenu.style.display = '';
    }
});
```
今天就大概這樣，下一篇就是最後的頁面了。
