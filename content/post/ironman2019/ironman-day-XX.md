---
title: "[鐵人賽DayXX] 簡單聊天室實作(1) - 建立聊天室版型"
date: 2018-10-03T21:07:58+08:00
draft: true
categories: [2019鐵人賽]
tags: [2019鐵人賽]
---
今天我們要來實作簡單的聊天室，大概要實作的東西包括基本的群組功能、大廳功能、個人訊息，只有這些感覺還不太夠，還會加上一些基本的聊天功能，例如Emoji表情文字，群組部分有點像是線上遊戲的開房間，一開始我們會進到聊天大廳，可以在大廳跟大家聊天，還有創建房間，架構大概像下方這張圖。
![chatroom](chatroom.jpg)
首先我們用第3天的專案當基底開始，沒有的人可以到這邊下載

# 引用Bootstrap4
因為我不想在CSS排版上下一堆功夫，所以直接引用Bootstrap4來排版用，我也不想抓下來，直接使用CDN吧！

引用CSS在`html head`
``` html
<link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
```
Bootstrap依賴的JQuery
```html
<script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js" integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
<script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js" integrity="sha384-ChfqqxuZUCnJSK3+MXmPNIyE6ZbWh2IMqE241rYiqJxyMiZ6OW/JmZQ5stwEULTy" crossorigin="anonymous"></script>
```

# 建立版型
首先來建立我們要的版型吧！

大廳基本上我想切成3塊，上方為房間列表，左下為大廳聊天室，右下為連線使用者列表及登入選單。

版面我用滿版的，所以套上普通的`container`就好，首先先套上`conteainer`
``` html
<div class="container">
    <!--聊天室內容-->
</div>
```
依照板塊配置我們主要分上(房間列表)和下(大廳聊天室+登入選單+連線使用者列表)，所以我們先定義2行`row`，順便給上下區塊名稱`container-top`和`container-bottom`
``` html
<div class="row container-top"></div>
<div class="row container-bottom"></div>
```
定義各區塊的高度，`container`跟瀏覽器高度一樣，上方60%下方40％，`container-top`
``` css
.container{
    height:100vh;
}
.container-top{
    height:60%;
    overflow-y: auto;
}
.conteainer-bottom{
    height:40%;
}
```



# 建立在線所有人的名單
首先來建立一個監控所有人在線的名單
