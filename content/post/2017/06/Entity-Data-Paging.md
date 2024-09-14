---
title: EF - 實作資料分頁
date: 2017-06-10 14:47:35
categories: "Entity Framework"
tags: ["Entity Framework", "Entity", "C#"]
---
# 前言
其實資料分頁在.net已經有很多好用的套件或是現成物件可以用了，例如WebForm的GridView和MVC的PagedList.Mvc，但是以前我還在寫PHP的時候，其實這些東西完全不需要使用這些東西，只要sql語法就能做到，所以這篇我寫紀錄一下不依賴這些東西就能完成的方法。

# 資料分頁在 PHP 的作法
有使用過PHP的工程師應該都是到MySQL可以使用Limit的方式來限制出現的資料數量來實作分頁。
例如以下語法假設我讀第3頁，一次讀10條資料，也就是讀第21~30條的資料
``` sql
SELECT * FROM Table LIMIT 20,10
```
簡化的規則變成以下這樣
``` sql
SELECT * FROM Table LIMIT (pageIndex * pageSize),pageSize
```
pageIndex：是當前分頁索引
pageSize：每頁的資料量
但是在SQL Server這邊沒有Limit這個描述，該怎麼做呢?

# 資料分頁在 .Net 的作法
## SQL
在SqlServer這邊沒有Limit那麼好用的方法，但是我們有Top和NOT IN混用也能用出差不多的效果，
``` sql
SELECT TOP(pageSize) * FROM Table 
WHERE id NOT IN (
    SELECT TOP(pageIndex * pageSize) FROM Table
    ORDER BY id DESC
    )
ORDER BY id DESC
```
雖然複雜點但是效果是差不多，這邊Google過還有蠻多方法的，以上只是其中一種。
## LINQ To Entity
LINQ則是先用Skip跳過不需要的資料，在使用Take限制取得的資料量，db為我的ADO.NET實體資料庫模型
``` cs
(from t in db.Table orderby t.Id descending select t)
.Skip(pageIndex * pageSize)
.Take(pageSize);
```
或是用Method
``` cs
db.table.OrderByDescending(t => t.Id).Skip(pageIndex * pageSize).Take(pageSize);
```
# 後記
其實這些寫法我都是用在API上比較多，大部分的人應該還是都依賴套件的寫法，寫起來會比較快速。


# 參考資料
- [http://blog.miniasp.com/post/2007/12/11/Paging-using-simple-T-SQL-in-SQL-Server.aspx](http://blog.miniasp.com/post/2007/12/11/Paging-using-simple-T-SQL-in-SQL-Server.aspx)
- [https://dotblogs.com.tw/kim66766/2011/04/10/sql-paging-method](https://dotblogs.com.tw/kim66766/2011/04/10/sql-paging-method)
