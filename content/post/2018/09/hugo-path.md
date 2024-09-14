---
title: "Hugo - 文件路徑問題"
date: 2018-09-15T20:19:23+08:00
categories: "Hugo"
tags: ["Hugo", "Git"]
---
# 前言
最近發現Hugo有些路徑問題，這篇記錄一下解決方法

# Hugo文件產生路徑
發現Hugo產生的路徑一定會變成小寫，所以圖片資料夾一定要全小寫，分類及Tags則是沒關係還是能打大寫。

## GitHub 大小寫不同的同名資料
這邊我也是最近才發現，原來以前Hexo沒這問題，所以push會變成2種名字依樣同時存在的問題，例如`Hexo`和`hexo`。
解決方法
``` shell
$ git mv -f Hexo ../hexo
```
此時hexo會被移動到外層，在手動拖回去就行了。

> 註：不曉得為啥Rename同層路徑會一直失敗，所以目標才會設在前一層


# 參考

- [https://www.peterdavehello.org/2014/05/change-filename-case-int-git-repository/](https://www.peterdavehello.org/2014/05/change-filename-case-int-git-repository/)