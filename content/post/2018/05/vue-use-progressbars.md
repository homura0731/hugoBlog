---
title: "Vue.js - 使用BootStrap4 Progress Bar"
date: 2018-05-14T21:20:59+08:00
categories: "Vue.js"
tags: ["Vue.js", "Bootstrap4"]
---
# 前言
這篇是我在IT邦幫忙第一個最佳解,也是第一個回答
做個紀錄當回憶

[https://ithelp.ithome.com.tw/questions/10189197](https://ithelp.ithome.com.tw/questions/10189197)

# 使用框架
- Vue.js 2.x
- BootStrap4

# 實作
## Bootstrap progress bars範例
先去Bootstrap4官方文件找出範例
``` html
<div class="progress">
  <div class="progress-bar" role="progressbar" style="width: 25%" aria-valuenow="25" aria-valuemin="0" aria-valuemax="100"></div>
</div>
```
仔細看會發現裡面有像是進度的數值`style="width: 25%"`

## Vue物件實作
先設定一個`barValue`當bar的進度值
然後再`vue mounted`時載入
避免他跑太快用`setTimeout()`延遲一秒
``` js
new Vue({
    el: '#app',
    mounted:function(){
        setTimeout(() => {
            this.barValue = '100%'
        }, 1000);
    },
    data: {
        barValue: '10%',
    }
})
```

## 繫結到html dom上
`style`改成`v-bind:style`
``` html
<div class="progress">
    <div class="progress-bar" role="progressbar" v-bind:style="{width: barValue}" aria-valuenow="25" aria-valuemin="0" aria-valuemax="100">
    </div>
</div>
```

## 完整範例

``` html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>Page Title</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/css/bootstrap.min.css" integrity="sha384-WskhaSGFgHYWDcbwN70/dfYBj47jz9qbsMId/iRN3ewGhXQFZCSftd1LZCfmhktB" crossorigin="anonymous">
</head>
<body>
    <div id="app">
        <div class="progress">
            <div class="progress-bar" role="progressbar" v-bind:style="{width: barValue}" aria-valuenow="25" aria-valuemin="0" aria-valuemax="100"></div>
        </div>
    </div>
              
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js" integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/js/bootstrap.min.js" integrity="sha384-smHYKdLADwkXOn1EmN1qk/HfnUcbVRZyYmZ4qpPea6sjB/pTJ0euyQp0Mk8ck+5T" crossorigin="anonymous"></script>         
    <script src="https://cdn.jsdelivr.net/npm/vue@2.5.16/dist/vue.js"></script>
    <script>
        new Vue({
            el: '#app',
            mounted:function(){
                setTimeout(() => {
                    this.barValue = '100%'
                }, 1000);
            },
            data: {
                barValue: '10%'
            }
        })
    </script>
</body>
</html>
```


