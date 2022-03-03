---
weight: 4
title: "Laravel 介紹"
date: 2022-03-02T10:33:45+08:00
lastmod: 2022-03-02T18:17:22+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["PHP", "框架", "Laravel", "實作","介紹"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

本章節會介紹什麼是 Laravel ，以及它有什麼特別之處，可以讓它在2015年被評為最受歡迎的 PHP  框架第一名，並說明為什麼要使用框架，最後再附上實作來作證。

<!--more-->

就像是 Laravel 官網的大標題，『 The PHP Framework for Web Artisans 為網頁藝術家創造的 PHP 框架 』，那再了解 Laravel 之前，先來介紹一下框架是什麼：

## 框架是什麼

* 框架 (Framework) 是一個被設計用來完成特定任務的規範，程式開發人員必須遵從這個規則來開發。
* 目前大多數的框架都是參考 MVC 架構的概念來做設計，原因是在早期開發網頁時，都是直接將 HTML 以及 PHP 混合再一起的方式來編寫，雖然開發上很方便，但在後面維護或是新增功能上，會十分不便利，例如：單純要修改網站畫面的元件時，還需要從混雜的程式碼中找到要修改的元件，也很容易不小心修改到其他的功能。
* 於是有人想到把這些各自的任務區分開來，MVC 架構分別代表的是 
	* M (Model) : 屬於資料的部分，可能是商用邏輯或是資料庫的存取。
	* V (View)：屬於顯示的部分，像是 HTML、CSS 等
	* C (Controllor)：會針對請求做出回應或是處理，例如從 Model 取出資料，並顯示在 View 上面

## Laravel 框架

Laravel 是基於上面所說的 MVC 架構打造的框架，並且設計出許多可以讓開發者更有效率的工具。

* Artisan 提供許多指令，讓你可以使用這些指令，來快速完成許多的任務。
	* Blade 的樣板系統，可以將程式碼與 HTML 頁面完全分離，讓你更專注在網站頁面的設計
	* Routing 的機制，簡單卻強大的管理網址與頁面的路徑
	* 利用 Controllor 將程式邏輯隱藏在背後
* Eloquent ORM 讓你再也不需要撰寫任何的 SQL 指令，就可以跟資料庫進行互動
* Migtation 工具，讓資料庫的遷移不再是惱人的一件事情

## PHP 常見框架對比

雖然本次介紹的主題是 Laravel 但我們也簡單介紹一下其他的框架優缺點，附上表格讓大家可以更清楚。

| 差別 | Laravel | Symfony | CodeIgniter | CakePHP | Zend 2 | 
| --- | :---: | :---: | :---: | :---: | :---: |
| 許可 License | MIT | MIT | BSD | BSD | MIT |
| 人氣排名 Popularity<br>(2022-03 Google 搜尋熱門程度)  | 第一名 | 第二名 | 第三名 | 第四名 | 第五名 | 
| 表現 | 慢 | 慢 | 快 | 慢 | 慢 |
| 模板 | Blade | Twig | PHP | 內建 | PHP |
| 資料庫 | MySQL <br> PostgreSQL <br> SQLite <br> SQL Server | MySQL <br> PostgreSQL <br> SQLite <br> SQL Server <br> Oracle | MySQL <br> PostgreSQL <br> SQLite <br> SQL Server <br> Oracle | MySQL <br> PostgreSQL <br> SQLite <br> SQL Server <br> Oracle | MySQL <br> PostgreSQL <br> SQLite <br> SQL Server <br> Oracle | 
| 物件關係對映 | 有 | Doctrine | 沒有內建 |  有 | Doctrine |
| 測試 | PHPUnit | PHPUnit | 內建 |  PHPUnit | PHPUnit |

### Laravel 

該框架可能是Web 開發人員中最受歡迎的框架。Laravel 是一個免費的開源PHP 框架，適用於Web 應用程序開發，且適用於移動應用程序場。(上面有介紹過，這邊就簡化)

**Laravel 框架優點**
* 易於學習
* 無縫數據遷移
* 在 PHP 社群中很受歡迎
* 支持 MVC 架構
* 大量學習素材 (文件、圖檔、影片教程)
* 模板引擎
* 簡單的單元測試

### Symfony

該框架是一個廣泛的PHP MVC 框架，目前Symfony 已經成為一個可靠和成熟的平台框架。

Symfony 非常穩定、文檔齊全、性能卓越。這些特點使Symfony 成為開發大型企業項目的完美選擇。

使 Symfony 成為 PHP 框架中獨一無二的特性之一是它的可重用 PHP 組件。使用可重用組件，開發時間減少了許多模塊，如表單創建、對象配置、模板等。

可以直接從舊組件構建，節約了大量成本。Symfony 易於在大多數平台上安裝和配置，並且可以獨立於數據庫引擎。它具有高度的靈活性，可以與 Drupal 等大型項目集成

**Symfony 框架優點**

* 官方長期技術支持
* 內置測試功能
* 豐富的框架內置功能
* 官方培訓課程和認證

### CodeIgniter

該框架可能是最適合開發動態網站的PHP 框架。它是一個非常簡單的輕量級PHP 框架。

它的大小只有2 MB 左右（包括文檔）。因此，CodeIgniter 本身俱有最小的佔用空間，它允許Web 開發人員添加第三方插件來開發更複雜的功能。

CodeIgniter 還提供了幾個預構建的模塊，用於為Web 開發創建健壯的、可重用的組件。由於設置過程簡單，這個PHP 框架非常適合初學者。

**CodeIgniter 框架優點**

* MVC 架構
* Top-Notch 錯誤處理
* 提供卓越的性能
* 包中提供了幾種工具
* 內置安全工具
* 優秀的文檔

### CakePHP

該框架對個人是完全免費，並提供付費的商業用途。它將幫助您開發功能豐富且視覺上令人印象深刻的網站。

CakePHP 起初是一個簡單而優雅的工具包，在過去的15 年裡它變得更加強大。由於它的CRUD（創建、讀取、更新和刪除）框架，CakePHP 是最容易學習的框架。

使用CakePHP 部署Web 網站是"小菜一碟"，只需要一個Web 服務器和CakePHP 框架的副本。

**CakePHP 框架優點**

* 插件和組件的簡易擴展
* 適當的類繼承
* 零配置
* 現代框架
* 支持AJAX
* 快速構建
* 內置驗證


### Zend 2

該框架是一個完整的面向對象的PHP 框架。這個PHP 框架是可定制的，對於需要添加項目特定功能的開發人員來說，這是一個好處。

Zend 構建於敏捷方法之上，可幫助開發人員為大型客戶創建、高質量的Web 應用程序的框架。它非常適合複雜的企業級項目，Zend 主要關注安全性、性能和可擴展性。

Zend 框架主要受大型IT 企業和銀行等金融機構的青睞。

**Zend 2 框架優點**

* MVC 組件
* 卓越的前端技術支持工具
* 大型開發者社區
* 簡單的雲API
* 支持第三方組件
* 數據加密
* 支持AJAX
* 會話管理


## 實作

{{< admonition type=info title="Laravel 官網" open=true >}}
我們是參考 [Laravel 官方網站](https://laravel.com) 的文件來進行實作，也可以參考台灣的  [ Laravel.tw](https://laravel.tw/) 來操作歐！
{{< /admonition >}}

### 環境設定

要安裝 Laravel 框架有一些系統的要求，要先安裝 PHP ，請參考[ PHP 官網的安裝教學](https://www.php.net/manual/zh/install.php)

安裝完後可以使用來檢查是否安裝成功

```sh
$ php --version                                                                                                                                      
PHP 7.1.33 (cli) (built: Jan 20 2022 04:04:37) ( NTS )
Copyright (c) 1997-2018 The PHP Group
```
<br>

再安裝 Composer ，請參考 [ Composer 官網的安裝教學](https://getcomposer.org/download/)


```sh
$ composer --version                                                                                                                           
Composer version 2.2.7
```
<br>
都安裝好後，我們就要來安裝 Laravel 框架囉，根據 Laravel 官網安裝步驟，先用 Composer 來安裝

```sh
$ composer global require "laravel/installer"
```
<br>
安裝後一樣先來檢查是否安裝成功

```sh
$ laravel --version                                                                                                                                 
zsh: command not found: laravel
```
<br>

發現 zsh 找不到 laravel 這個命令，那我們看一下官網怎麼說

`Make sure to place the $HOME/.composer/vendor/bin directory (or the equivalent directory for your OS) in your $PATH so the laravel executable can be located by your system.`

<br>
代表我們要將 Laravel 的目錄，放到 $PATH ，系統才可以使用 laravel 來執行檔案。

以 macOS 來說， Laravel installer 的目錄都會放在 `$HOME/.composer/vendor/bin` ，我們在根目錄建立一個 .bash_profile 的檔案來放置我們的 $PATH。


```sh
export PATH="$HOME/.composer/vendor/bin:$PATH"
```

<br>
輸入上面的 PATH ，記得儲存離開後要用這個指令來更新一下檔案

```sh
$ source .bash_profile
````

我們再次檢查是否安裝好 Laravel 

```sh
$ laravel --version                                                                                                                                 
Laravel Installer 2.3.0
```
<br>

本次配置版本如下

* macOS 11.6
* PHP 7.1.33
* Composer 2.2.7
* Laravel Installer 2.3.0


### 新增專案

安裝成功後，就可以開始來使用 Laravel 框架生成檔案囉！要怎麼做呢？就接著看下去吧

可以使用 Laravel 來新增專案

```sh
$ laravel new 專案名稱
```
<br>

也可以透過 composer 指令 (可以指定框架版本，那我們這次使用的是5.4版本)

```sh
$ composer create-project --prefer-dist laravel/laravel 專案名稱 "5.4.*"
```
<br>

完成後就進入該專案目錄底下，接著可以執行該指令來檢查框架版本

```sh
$ php artisan -V
Laravel Framework 5.4.36
```
<br>

{{< admonition type=tap title="小提醒" open=true >}}
Laravel 框架的版本，取決於你的 PHP 版本，所以要找相對應的歐！，使用 Laravel 5.4 跟 PHP 8.1 ，他會說PHP 版本太新，無法安裝。
{{< /admonition >}}

### Laravel 資料夾與檔案介紹

我們可以看到這個專案目錄下，Laravel 幫我們生成了許多資料夾以及檔案，接著來簡單說明每一個資料夾與檔案的功能與用途吧

* app：主要放置 Controller 的地方，提供網站的應用處理流程，包括處理用戶的行為和資料 Model 上的改變等事件之類別方法，提供給 view 來呼叫。透過 laravel 之架構也可透過 controller 輕易建構 RESTful API。
* boostrap：內含之 app.php 為將此框架初始化及建構起來之程式。
* config：內含此專案網站之環境設定、資料庫設定等設定程式。
* database：放置資料庫設定 (Model)。
* public：放置網站入口，透過 index.php 導入從 route 設定之首頁
* resources：放置 view 的資源，用以呈現網站頁面
* routes：放置所有專案 route 的設定，其中較常用之 web.php 做為主要頁面導向及與 controller 的溝通，而 api.php 則作為專案提供 api 的設定。
* storage：主要放置些程式生成的檔案如頁面樣板 (Blade templates)、系統記錄 (logs)、file caches、file based sessions 等資料。
* tests：放置測試用例 (Test Case)。
* vendor：放置透過 composer 下載管理的套件。
* artisan：可於專案中透過 "php artisan" 執行 laravel 設計好的基本操作行為，如建立 controller 等動作。
* composer.json：管理專案使用套件，詳細用法見 composer 官方說明。
* composer.lock：鎖定專案使用套件版號。
* package.json：管理 npm 套件，主要提供給前端使用。
* phpunit.xml：phpunit 測試設定檔，可規範執行單元測試之範圍，做批量測試。
* webpack.mix.js：build 設定檔，幫助我們將 resources 中之 js 及 sass 等前端設定檔 compile 成 js、css 等檔案並 deploy 到 public 中供頁面使用。

{{< admonition type=quote title="文章參考來源" open=true >}}
下面教學流程是參考ReccaChao [ Laravel 6.0 初體驗！怎麼用最新的 laravel 架網站！](https://ithelp.ithome.com.tw/articles/10213294) 的文章下去說明以及學習，在學期過程中再加上自己的理解以及不同範例的筆記來分享給大家，大家也可以先去瀏覽大大的文章，再回來呦XD
{{< /admonition >}}

### 建立第一個 Laravel 網頁

我們經過千辛萬苦，將 Laravel 給安裝好，也設定好它所需的環境參數，打開 IED 準備開始學習如何建立第一個 Laravel 網頁吧！

在開始說明前，還要先下一個指令，在介紹 Laravel 專案下的資料夾以及檔案中，其中一個 "Artisan"，是 Laravel 專用的指令工具，可以幫我們處理很多事情。 

像是我們現在要啟動內建的伺服器來瀏覽我們的網站，這時我們要下

````sh
$ poser global requirephp artisan serve
Laravel development server started: <http://127.0.0.1:8000>
[Thu Mar  3 15:13:07 2022] 127.0.0.1:52345 [200]: /favicon.ico
````

<br>
這個伺服器會在這個終端機下被建立，屬於這個專案的伺服器，如果想要關閉，可以使用 Ctrl + C 來關閉。

{{< admonition type= tap title="小提醒" open=true >}}
由於此伺服器是專屬於該專案的伺服器，所以下指令時，也要在該專案的目錄下歐！
{{< /admonition >}}

如果想要使用特定的 Port ，可以在後面加入此參數，就可以依照設定的 Port 來當作我們伺服器的位子囉！(預設是8000 Port)

````sh
$ php artisan serve --port=7777
Laravel development server started: <http://127.0.0.1:7777>
[Thu Mar  3 15:45:23 2022] 127.0.0.1:54206 [200]: /favicon.ico
````
<br>

#### 維護模式

當我們想要修改系統或是資料庫的欄位時，就可以將該網站設定成維修模式。Laravel 讓這個工作變得很容易，只需要用以下兩個指令來控制

啟動維護模式
```sh
$ php artisan down --message="Upgrading Database" --retry=60
```

<br>
還可以設定一些參數， --message 是用來顯示或紀錄客製化訊息， --retry 用來當作HTTP head 的 Retry-After 的值

<br>
{{< image src="/images/laravel/error503.png"  width="700" caption="Laravel 維護模式顯示頁面" src_s="/images/laravel/error503.png" src_l="/images/laravel/error503.png" >}}
<br>
關閉維護模式

```sh
$ php artisan up
```

{{< admonition type=abstract title="維護模式顯示模板" open=true >}}
維護模式顯示的預設模板放置在 resources/views/errors/503.blade.php。你可以自由的根據需求修改。
{{< /admonition >}}
<br>
#### routes 路由

我們要來建立我的第一個網頁，網頁的其中一個關鍵就是路徑，那我們網站的路徑，在 Laravel 裡面，都放置在 routes/ 裡面，打開後可以看到4個檔案有

* api.php：我們在做前後端分離的專案時會使用這個檔案。
* channels.php：和 Broadcast 有關係，這個是 Laravel ，不太常使用，所以先跳過，之後有碰到再來說明。
* console.php：這個和我們指令有關，像是我們上面介紹的 php artisan ，這個檔案就是和這個部分有關。
* web.php：這個和我們現在要說明的路徑有關，我們在瀏覽器打的網址，前面是 domain name，那在 domain 之後的字串，會在我們這個檔案裡面來定義哪些字串要導向哪一個流程或是檔案。

##### web.php

那我們就先來針對 web.php 來做介紹，我們剛剛使用指令開啟的伺服器網址是 ```http://127.0.0.1:8000```，使用瀏覽器進入後，可以看到下面這個 Laravel 預設的頁面(此為 Laravel 5.4版本，新版的好像不太一樣)

{{< image src="/images/laravel/laravel_welcome.png"  width="700" caption="Laravel 預設首頁" src_s="/images/laravel/laravel_welcome.png" src_l="/images/laravel/laravel_welcome.png" >}}

那我們開啟 routes/web.php 檔案中，可以看到下面這一段預設的路由

```php
Route::get('/', function () {
    return view('welcome');
});
```
<br>

這段的意思就是代表，如果我們網址沒有輸入其他的目錄，就會顯示 welcome 這個網頁，那 welcome 這個顯示的畫面，就放在 ```resources/view``` 底下名為 welcome.blade.php 的檔案中，打開來看就是簡單的 HTML。

那現在我們了解路由的含義，現在換我們來實作一個看看，首先我們希望網址列輸入 ```https://127.0.0.1:8000/hello-world``` 可以出現 ```hello world``` 的字樣，那我們可以到 web.php 中，新增下面這個路由

```php
Route::get('/hello-world', function () {
    return view('hello-world');
});
```
<br>

再到到 ```resources/view``` 新增 hello-world.blade.php 檔案，簡單輸入 HTML 格式，如下

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1> hello ~ pinyi </h1>
</body>
</html>
```
<br>

使用瀏覽器在網址列輸入 ```http://127.0.0.1/hello-world``` ，就可以看到顯示 ```hello ~ pinyi ``` 的字樣囉～

除了回傳網頁來做顯示以外，也可以單獨回傳字串，並帶入參數歐，我們來試試看要怎麼做。首先我們先將路由修改成下方，讓我們在 hello-world 後面可以輸入我們的名稱，並且顯示在畫面上

```php
Route::get('/hello-world/{name}', function ($name) {
    return 'hello ~ ' . $name;
});
```
<br>

我們在網址列輸入 ```http://127.0.0.1/hello-world/pinyi``` 就會顯示 ```hello ~ pinyi``` ，很神奇吧，只要在括號中 {} 輸入你要的參數，並且後面的 function 也要帶入參數，就可以將資料給帶進去了 !

##### api.php

我們可以看到 api.php 檔案也是使用 Route 來運作，如下

```php
Route::middleware('auth:api')->get('/user', function (Request $request) {
    return $request->user();
});
```
middleware 就是一種過濾或是防火牆的概念，這個我們後續會再說明。我們也在此新增一個跟上面一樣的 hello-world，如下

```php
Route::get('/hello-world', function () {
    return 'Hello World ~';
});
```

在 api.php 比較特別的部分是需要在字串前面加入 api ，因為 api.php 就是預設讓我們來放 api 的地方，如範例 url:{domain}/api/hello-world，那就會回傳 ```hello world ~```

#### Database 資料庫

我們在 [Laravel 框架](https://pin-yi.me/laravel/#laravel-框架) 部分有提到 Laravel 支援 MySQL、PostgreSQL、SQLite 、SQL Server 資料庫，本次介紹使用 MySQL 來做說明。

首先我們要先確認資料庫是否與 PHP 有所連結，我們先打開 ```.env``` 這個隱藏檔來看，大約在第8行有一段關於資料庫的敘述

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=secret
```

<br>


## 測試

寫完程式後，除了自己一個一個去測試，也可以寫成程式來測試是否有錯誤。會在此談到測試，是因為自動測試變得簡單也是 Laravel 這個框架的一個重要特性。

那我先來說明一下什麼是自動測試

* 簡單來說，常見的測試，就是我們在完成新功能後，會一個一個實際去測試我們寫的功能，是不是跟我們預想的那樣子運行。
* 一開始網站還小，功能不多時，還可以應付。但當網站越來越龐大，功能越來越多後，或是要使用不同權限帳號來檢查所有功能，會很辛苦的。

幸好，我們可以用自動測試的程式來解決這些瑣事！

* 如果我們每次為每個功能撰寫程式時，都寫另一段小程式來測試這個功能。
* 隨著時間過去，需求的改變，雖然功能越來越多，但是相對應的，測試這些功能的程式也會越來越多，並且不管這個功能是多久之前開發的，每個功能都有相對應的測試。

### 如何運行自動測試

在 PHP 的世界，自動測試通常使用 [PHPUnit](https://phpunit.de/) 這個工具。然後 Laravel 在安裝時，也自動幫我們安裝這個工具了～
Laravel 幫我們把 PHPUnit 安裝在 ```vendor/``` 裡面，我們來看看要怎麼使用它吧！使用的指令如下

```sh
$ vendor/phpunit/phpunit/phpunit
PHPUnit 5.7.27 by Sebastian Bergmann and contributors.

..                                                                  2 / 2 (100%)

Time: 146 ms, Memory: 10.00MB

OK (2 tests, 2 assertions)
```
<br>

奇怪，我們什麼都還沒做，為什麼會有兩個程式已經通過測試了，是因為 Laravel 預設兩個檔案來當測試的案例，分別位於 ```tests/Feature/ExampleTest.php``` 以及 ```tests/Unit/ExampleTest.php```  (單元測試)，我這邊就簡單說明 ```Feature/ExampleTest.php``` 這個測試程式做哪些事情

```php
<?php

namespace Tests\Feature;

use Tests\TestCase;
use Illuminate\Foundation\Testing\WithoutMiddleware;
use Illuminate\Foundation\Testing\DatabaseMigrations;
use Illuminate\Foundation\Testing\DatabaseTransactions;

class ExampleTest extends TestCase
{
    /**
     * A basic test example.
     *
     * @return void
     */
    public function testBasicTest()
    {
        $response = $this->get('/');

        $response->assertStatus(200);
    }
}
```

<br>

他檢查如果連線到 ```/``` ，HTTP Status 有沒有回傳 200 ， 這個測試功能就是在檢查首頁是否存在 ～

### 實作測試程式

大概了解原理後，一樣我們來實作看看，我們想要連線到 ```hello-world/```，HTTP Status 會回傳 200 ，以及網站內要看到『 hello world ~ 』的文字。那我們輸入指令

```sh
$ php artisan make:test HelloWorldTest
Test created successfully.
```
<br>

Laravel 自動幫我們在 ```tests/Feature``` 底下新增了 HelloWorldTest.php 檔案，內容如下(框架版本5.4)

```php
<?php

namespace Tests\Feature;

use Tests\TestCase;
use Illuminate\Foundation\Testing\WithoutMiddleware;
use Illuminate\Foundation\Testing\DatabaseMigrations;
use Illuminate\Foundation\Testing\DatabaseTransactions;

class HelloWorldTest extends TestCase
{
    /**
     * A basic test example.
     *
     * @return void
     */
    public function testExample()
    {
        $this->assertTrue(true);
    }
}
```

我們將他預設的 testExample() 的內容改成

```php
    public function testExample()
    {
        $response = $this->get('/hello-world');

        // 如果連線 hello-world/，HTTP Status 應該要顯示 200
        $response->assertStatus(200);
        
        //連線到網頁內，應該要可以看到「『 hello world ~』文字
        $response->assertSee('hello world ~');
    }
```

來運作一下吧，一樣使用同樣的指令來做測試，順便讓大家看一下測試錯誤的錯誤訊息吧！


```sh
$ vendor/phpunit/phpunit/phpunit
PHPUnit 5.7.27 by Sebastian Bergmann and contributors.

.F.                                                                 3 / 3 (100%)

Time: 200 ms, Memory: 10.00MB

There was 1 failure:

1) Tests\Feature\HelloWorldTest::testExample
Expected status code 200 but received 404.
Failed asserting that false is true.

FAILURES!
Tests: 3, Assertions: 3, Failures: 1.
```

<br>

我故意在 routes 下沒有 ```hello-world/``` 路徑，所以返回顯示 404 ，而不是 200 ，因此，就可以知道要怎麼去修改程式囉  !

## 參考資料

[PHP與Laravel簡介](https://ithelp.ithome.com.tw/articles/10237537)

[比較 PHP 網頁框架
](https://opensourcedoc.com/blog/comparing-php-web-frameworks/)

[Laravel 從入門到放棄 (一) – 透過 composer 架設 laravel 網站](https://christmasq.wordpress.com/2018/12/04/php-laravel-%E5%BE%9E%E5%85%A5%E9%96%80%E5%88%B0%E6%94%BE%E6%A3%84-%E4%B8%80-%E9%80%8F%E9%81%8E-composer-%E6%9E%B6%E8%A8%AD-laravel-%E7%B6%B2%E7%AB%99/)

[Laravel 6.0 初體驗！怎麼用最新的 laravel 架網站！](https://ithelp.ithome.com.tw/articles/10213294) 

[Laravel Route(路由)](https://ithelp.ithome.com.tw/articles/10217731)