---
weight: 4
title: "如何在 Nginx 下實作第一個 PHP 留言板 RESTful API"
date: 2022-02-24T22:24:24+08:00
lastmod: 2022-02-24T22:24:4+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["PHP", "RESTful API", "介紹"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

由於網路上文章幾乎都是使用 apache 來實作第一個 PHP RESTful API  (留言板)， 也較沒有中文的版本，剛好這次也在學習，所以把學習的紀錄分享給大家，本次教學內容採用的是
<!--more-->

* macOS 11.6
* Nginx 1.21.6
* PHP 8.1.2
* 無框架

來做教學以及示範，那我們先來簡單了解一下什麼是RESTful API：

REST ，指得是一組架構約束條件和原則，符合 REST 設計風格的Web API 稱為 RESTful API，主要以下面三點為定義

* 直觀簡單的資源網址URL 比如：http://example.com/resources
* 傳輸的資源：Web 服務接受與返回的類型，比如：JSON、XML 
* 對資源的操作：Web 服務在該資源上所支持的請求方法，比如：POST、GET、PUT、DELETE

### 1. 首先要先來修改我們的 Nginx.conf

我們這邊就直接對架設好的 Nginx 設定檔來修改，如果不清楚要怎麼架設的，這方面網路上蠻多文章，如果還是不清楚，可以留言告訴我，我在另外寫一篇文章來介紹環境安裝。

我們待會的實作，會將希望將

```url 
http://localhost/api/index.php?message_board=all
```
變成

```url 
http://localhost/api/message_board
```
來實現我們在介紹時所說的直觀簡單的資源網址，我說明一下上面網址的關係，http://localhost 是因為我們本地端運行，如果已經上線的，那就是你自己的網址；/api 是我來放這支 api 的目錄，他位於網頁的根目錄下方(詳細的配置下方會附上)， index.php 是我來放這支 api 的網頁，會在 include request 及 responce 進來，後面的 message_board 是我們這次的資源，後面可以接我想要處理的參數

那要怎麼達成讓網址變成我們想要呈現的直觀簡單的網址呢，這時候我們就要使用 Nginx.conf 來做設定，網路上的文章比較多的是 apache 的 htaccess ，我一開始還以為 Nginx 也可以使用 htaccess ，試了半天才知道，在 Nginx 要改用 Nginx.conf 來設定 ， 那我下方會附上我原本的 .htaccess 以及轉換後的 conf ，網路上蠻多工具可以線上轉換，我推薦可以用 [Apache htaccess to Nginx converter
](https://winginx.com/en/htaccess) ，那接下來我一步一步來介紹

* Apache .htaccess 檔案

```.htaccess
RewriteRule ^/api/message_board$   /api/index.php?message_board=all [nc,qsa]
RewriteRule ^/api/message_board/(\d+)$   /api/index.php?message_board=$1 [nc,qsa]
```
這是我會修改部分的 Nginx.conf ，可以看到最後兩條 location，這邊就是我們轉換過來的設定


* Nginx.conf 檔案(片段) 

```conf
    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm index.php;
        }

		location = /api/message_board {
		  rewrite ^(.*)$ /api/index.php?message_board=all;
		}
		
		location /api {
		  rewrite ^/api/message_board/(\d+)$ /api/index.php?message_board=$1;
		}
```

他的意思代表的我們網址路徑是在 /api/message_board ，我們讓原本 /api/index.php?message_board 這段變成 /api/message_board/ 的設定，設定好記得先下  ```sh sudo nginx -t ``` 指令來檢查一下Nginx.conf檔案是不是都正確，再用 ```sh sudo nginx -s reload ``` 指令重新啟動 Nginx  (是否成功，後面會帶到如何做測試，就繼續一起往下看吧！)


## 參考資料

[PHP實現RESTful風格的API實例](https://www.cnblogs.com/luyucheng/p/6016801.html)

[[XAMPP][PHP] 製作Restful API
](https://cg2010studio.com/2016/08/06/xamppphp-%E8%A3%BD%E4%BD%9Crestful-api/)