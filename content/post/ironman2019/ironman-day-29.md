---
title: "[鐵人賽Day29] 把signal架設到Nginx或IIS"
date: 2018-11-05T20:35:33+08:00
draft: true
tags: [2019鐵人賽]
---
既然實作都做完了，卻只能單機自己使用好像怪怪的，至少要能開放自己的內網使用才對，所以今天要來教大家怎麼把自己的`signalR`部署到`Apache`和`IIS`
# 安裝.Net Core RunTime
首先先到[官網下載](https://www.microsoft.com/net/download)`Runtime`

# 發行自己的ASP.NET Core程式
發行非常簡單，使用`donet publish`就行，參數設定我們使用release，體積比較小
``` shell
$ dotnet publish -c Release
```
> 這邊的`-c`等於`-configure`可以簡化成`-c`

之後成功後在你的`Bin`底下找到`Release\netcore版本`資料夾，裡面有個`publish`就是發行後的網站檔案

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

# 架設到Apache
Apache我們使用`macOS`示範，`macOS`原本就有內建Apache，開啟終端機輸入以下指令啟動
``` shell
$ sudo apachectl start
```
輸入`loacalhost`會看到`It's Work`

然後開啟`finder`使用快捷鍵`cmd`+`shift`+`g`輸入`/etc/apache2`，之後開啟`httpd.conf`這個文件

搜尋Vitural Host，然後把Include前面井字註解拿掉
```
# Virtual hosts
Include /private/etc/apache2/extra/httpd-vhosts.conf
```



# 架設到IIS



# 參考
> [Savorboard的CNBlog](https://www.cnblogs.com/savorboard/p/dotnet-core-publish-nginx.html)
> [极客世杰的CNBlog](https://blog.csdn.net/github_33644920/article/details/51733436)