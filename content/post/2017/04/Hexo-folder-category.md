---
title: Hexo - 文章分類方法
date: 2017-04-16 14:40:48
categories: "Hexo"
tags: ["Hexo"]
---
# 前言
雖然Hexo這框架我才用幾天，但發現文章都集中在source/_post 底下
這樣文章只要一多起來一定很難管理，所以這篇就來記錄一下管理的方法

# 管理方法
## 官方文件的做法
官方建議在產生文章加上日期在.md檔案名稱上
去_config.yml底下修改new_post_name設定
``` yml
new_post_name: :year-:month-:day-:title.md
```

## 個人做法
官方那樣做確實比較好管理，但是我還是覺得都在同一個資料夾下實在太亂了
所以自己想到一個比較好的方法就是用改成資料夾管理，一個月跟一個資料夾，這樣分類方便多
首先得先把預設設定日期資料夾分類先關閉，一樣_config.yml底下
``` yml
new_post_name: :year/:month/:title.md
```
如果沒有那月份的資料夾指令輸入完會自動建立



