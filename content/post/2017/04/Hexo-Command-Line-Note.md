---
title: Hexo - 指令和設定筆記
date: 2017-04-15 10:31:14
categories: [Hexo]
tags: [Hexo,bash]
---
# 前言
首先先來寫一篇Hexo常用指令集和一些常會去調的設定，不然老是去翻官方文件也蠻麻煩的

# 常用指令集
啟動伺服器
``` bash
$ hexo server 
```
建立新文章
``` bash
$ hexo new "文章名"
```
產生後並佈署到github(第一次用時一直只有產生，結果因為檔案沒更新一直傳不上去..)
``` bash
$ hexo deploy --generate
```

hexo套件安裝(例如我的themes需要hexo-until)
``` bash
$ npm install --save hexo-util
```

複製themes
``` bash
$ git clone github位置 themes/主題資料夾名稱
```

# 設定相關
## 新增分類頁面(標籤同方法)
1.新增一個頁面
``` bash
$ hexo new page categories
```
2.設定頁面屬性
``` yml
type: "categories"
```
3.去thems資料夾的_config.yml，設定頁面
``` yml
categories: /categories
```

## 佈署
佈署到github時，需要先設定根目錄下的_config.yml
``` yml
deploy:
  type: git
  repo:
    github: https://github.com/github帳號/github帳號.github.com.git
  branch: master 
```
成功的話會跳出github登入視窗，登入後應該就成功了


# 後記
之後還有需要會在不定時更新