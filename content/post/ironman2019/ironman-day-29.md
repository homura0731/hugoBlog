---
title: "[鐵人賽Day29] 部屬Signal到Server上"
date: 2018-11-11T07:32:33+08:00
categories: [2019鐵人賽]
tags: [2019鐵人賽, SignalR, ASP.NET Core]
---
既然實作都做完了，卻只能單機自己使用好像怪怪的，至少要能開放自己的內網使用才對，所以今天要來教大家怎麼把自己的`signalR`部署到`Apache`和`IIS`
# 安裝.Net Core RunTime
首先先到[官網下載](https://www.microsoft.com/net/download)`Runtime`

> 只有macOS不需要裝，因為Runtime根本沒macOS版....

# Publish自己的ASP.NET Core程式
發行非常簡單，使用指令`donet publish`就行，參數設定我們使用release，體積比較小
``` shell
$ dotnet publish -c Release
```
> 這邊的`-c`等於`-configure`可以簡化成`-c`

之後成功後在你的專案裡面的路徑`Bin\Release\netcore版本`資料夾，會有個`publish`資料夾就是發行後的網站檔案，體積非常的小，這時就看出`.NET Core`的好處

由於官網文件只教了幾個平台範本，所以在架設之前我來說一下我試過的平台及Server軟體，MSDN文件就只介紹`Windows IIS`和`Linux Nginx`和`Linux Apache`三個，我就偏偏要用`macOS`來架設哈哈

|作業系統|Server軟體|是否成功|成功狀況
|-------|----------|-------|--------
|Windows 10|IIS|O|完美執行
|Windows 10|Apache|O|可以執行，但是websocket啟用不了，`SignalR`會改成SSE的方式
|macOS|Apache|X|
|macOS|Nginx|O|可以執行，版本太舊，websocket似乎不支援，`SignalR`會改成SSE的方式

結果`macOS`在結論上都是失敗的，`Nginx`雖然有成功代理上，但是卻版本太舊好像不支援`WebSocket`，連`Long Polling`都無法，真是怪了


# 架設到 Nginx
Nginx的部分我用`macOS`示範，首先先用`Homebrew`安裝Nginx，如果沒裝過`Homebrew`到[這邊安裝](https://brew.sh/index_zh-tw)
``` shell
$ brew install nginx
```
> 安裝可能會遇到權限問題裝不了，這時候使用`sudo chown -R $(whoami) /usr/local/share/man/man3`就OK了

裝完後使用指令看看有沒有版本好，有就代表安裝成功

``` shell
$ nginx -v
```
然後我們要來把發行後的檔案Run起來，先開終端機再到上面說的publish資料夾底下，跑以下指令
``` shell
$ dotnet <專案名>.dll
```
然後去`https://localhost:5001`底下看是不是有在跑

## 設置nginx代理配置
這邊到`/usr/local/etc/nginx`底下，找不到的話可以用Finder的功能，前往然後選前往檔案夾，之後輸入路徑前往
![GoPath](GoPath.png)

然後找到`nginx.conf`，找到Server節點，裡面有`listen 8080`，然後改成以下這樣
```
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;

    #access_log  logs/host.access.log  main;

    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
```
然後再執行以下指令就成功了
``` shell
$ sudo nginx -s reload
```
我這邊遇到`nginx: [error] invalid PID number "" in "/usr/local/var/run/nginx/nginx.pid"`這個錯誤訊息
查了一下只要多跑個指令就OK，然後再跑一次reload
``` shell
$ sudo nginx -c /usr/local/etc/nginx/nginx.conf
$ sudo nginx -s reload
```
成功後就能開起瀏覽器輸入`localhost`就能看到自己的`ASP.NET Core`程式
![nginxStart](nginxStart.png)

> 註：建議改塗鴉牆範例，因為文件分享有用到websocket的一部分，導致無法同步

但是要讓內網連近來還需要多個步驟，先使用停止指令
``` shell
$ sudo nginx -s stop
```
使用以下指令啟用，讓外部連結進來，
``` shell
$ sudo brew services start nginx
```
然後會問你接受傳入的網路連線嗎？選接受

![NginxServiceStart](NginxServiceStart.png)


然後就能讓內網連線了，不過`brew`安裝的`nginx`似乎沒支援`websocket`，所以`SignalR`會改幫你使用`SSE(Server Sent Events)`的方式，看來還是得要`Linux`比較好XD。

# 架設到Apache
Apache我們使用`Win10`示範，首先下載[Xampp](https://www.apachefriends.org/zh_tw/index.html)，開始安裝，他會附加一堆其他東西，我們只有`Apache`就好，`php`不能取消就讓它安裝吧!

![xampp](xampp.png)
然後會問要不要安裝`Bitnami`請把勾勾取消

![xampp2](xampp2.png)

安裝完打開，會問防火牆要不要允許通過，選是，然後打開`localhost`就能看到xampp的簡介畫面

## 設置Apache代理
然後我們要來修改一下設置的文件，先到路徑`C:\xampp\apache\conf`底下，使用`VS Code`打開`httpd.conf`，然後把下面的模組前面的`#`字註解拿掉，請用搜尋的方式找出來
```
LoadModule proxy_http_module modules/mod_proxy_http.so
```
然後到`C:\xampp\apache\conf\extra`底下，開啟`httpd-vhosts.conf`，在最下方加入
```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

在終端機目錄移動到`publish`底下，然後使用`dotnet`啟用他
``` shell
$ cd bin/Release/netcoreapp2.1/publish
$ dotnet <專案名>.dll
```
最後`Apache`重啟，這樣就成功啦！

測試結果是`Websockt`握手失敗，改成使用`SSE`的方式，還是能夠同步動作就是....

> 註：其實網路上很多設定我都使用過，包括`mod_proxy_wstunnel`模組啟用，設定ws協定通過，都試過沒有用，可能真的要`Linux`的`Apache`的才行。

# 架設到IIS
我使用`Win10`來邦範例，首先開啟以下位置 控制台 > 程式和功能 > 開啟或關閉Windows功能 ，然後把以下紅框功能打勾

![IIS_Setup](IIS_Setup.png)

然後把發行的的檔案丟進以下路徑`C:\inetpub\wwwroot`，整包publish丟過去，然後改名`TestWeb`


然後你應該就能找到你的IIS管理員了，然後把IIS管理員開啟，選左方的應用程式集區，再右邊新增應用程式集區，名稱可以隨便訂，我使用`.NET Core`當名稱，然後`.NET CLR版本`選擇`沒有 Managed程式碼`，`立即啟用應用程式集區`打勾後確定。

![IIS_NewAppSet](IIS_NewAppSet.png)

再來左邊的站台右鍵，新增網站，站台名稱隨便，應用程式集區選鋼鋼你新增的，路徑選鋼鋼丟進去的`C:\inetpub\wwwroot\TestWeb`，剩下都不用調整

![IIS_NewWeb](IIS_NewWeb.png)

確定之後會跳出提示，因為有預設的站台是用`80port`，我發現內網涉其他port會連不到，應該是要調特別設定，所以這邊我們選是

![IIS_Warning](IIS_Warning.png)

然後把左邊`Defalut Web Site`右鍵 > 管理網站 > 停止，再把剛新開的網站啟動，這樣就完成啦!

這樣就可以使用其他機器從內網`IP`連進你的網站了!

做完這篇的感想是`.NET Core`能跨平台真棒，今天大概就這樣啦!~~

# 參考
- [Savorboard的CNBlog](https://www.cnblogs.com/savorboard/p/dotnet-core-publish-nginx.html)
- [极客世杰的CSDN Blog](https://blog.csdn.net/github_33644920/article/details/51733436)
- [Yusan的CSDN Blog](https://blog.csdn.net/y505772146/article/details/80219690)
- [官方文件](https://docs.microsoft.com/zh-tw/aspnet/core/host-and-deploy/linux-apache?view=aspnetcore-2.1)