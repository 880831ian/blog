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

####  Laravel 框架優點
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

####  Symfony 框架優點

* 官方長期技術支持
* 內置測試功能
* 豐富的框架內置功能
* 官方培訓課程和認證

### CodeIgniter

該框架可能是最適合開發動態網站的PHP 框架。它是一個非常簡單的輕量級PHP 框架。

它的大小只有2 MB 左右（包括文檔）。因此，CodeIgniter 本身俱有最小的佔用空間，它允許Web 開發人員添加第三方插件來開發更複雜的功能。

CodeIgniter 還提供了幾個預構建的模塊，用於為Web 開發創建健壯的、可重用的組件。由於設置過程簡單，這個PHP 框架非常適合初學者。

#### CodeIgniter 框架優點

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

#### CakePHP 框架優點

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

####  Zend 2 框架優點

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
composer global require "laravel/installer"
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
source .bash_profile
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

我們可以看到這個專案目錄下，Laravel 	幫我們生成了許多資料夾以及檔案，接著來簡單說明每一個資料夾與檔案的功能與用途吧

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




## 參考資料

[PHP與Laravel簡介](https://ithelp.ithome.com.tw/articles/10237537)

[比較 PHP 網頁框架
](https://opensourcedoc.com/blog/comparing-php-web-frameworks/)

[Laravel 從入門到放棄 (一) – 透過 composer 架設 laravel 網站](https://christmasq.wordpress.com/2018/12/04/php-laravel-%E5%BE%9E%E5%85%A5%E9%96%80%E5%88%B0%E6%94%BE%E6%A3%84-%E4%B8%80-%E9%80%8F%E9%81%8E-composer-%E6%9E%B6%E8%A8%AD-laravel-%E7%B6%B2%E7%AB%99/)