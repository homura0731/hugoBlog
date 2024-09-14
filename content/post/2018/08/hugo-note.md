---
title: "Hugo - Hugo使用筆記"
date: 2018-08-15T00:01:09+08:00
categories: "Hugo"
tags: ["Hugo"]
---

# 前言
記錄一下使用筆記，因為還在摸索，不定時更新

# 指令
## 新增文章
hugo原生是產在posts底下，even主題是post
``` shell
$ hugo new post/文章名稱.md
```

## 新增頁面的方法
直接產生在Content底下會變成新頁面
``` shell
$ hugo new <頁面名稱.md>
```

## 發行檔產生
``` shell
$ hugo -t <主題名稱>
```
# Hugo文章標籤
## 草稿模式
如果開啟的話，發行時是不會看到的
``` md
draft: true
```
## 分類和標籤
跟Hexo有點不太一樣，他是用陣列的方式
``` md
categories: [Hugo,Blog]
tags: [Hugo,Blog]
```
## 文章產生的預設設定
在`archetypes/default.md`底下有預設文本
可以在那邊調整預設設定