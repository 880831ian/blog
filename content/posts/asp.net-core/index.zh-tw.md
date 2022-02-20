---
weight: 4
title: "ASP.NET Core介紹"
date: 2022-01-03T19:34:11+08:00
lastmod: 2022-01-03T19:34:11+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["ASP.NET Core", "介紹"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

.Net Core 是微軟新世代開發技術，不但實施了.NET跨平台開發與執行，同時也將框架大改造。

<!--more-->

## 介紹

.Net Core 是微軟新世代開發技術，不但實施了.NET跨平台開發與執行，同時也將框架大改造。


* 優勢
	* 跨平台 (可運行於Windows、Mac OSX、Ubuntu Liunx ) 是第一款Microsoft有跨平台能力的Ｗeb 開發框架，並支援雲端。
	* 跨架構一致性（在x64、x86、ARM、ARM64不同處理器架構執行，仍然保持相同）
	* 彈性部署（主機可用IIS、Apache、Docker）。
	* 一種用於MVC和Web API的統一編程模型。
	* 開放原始碼（使用MIS和Apache2授權的開放原始碼）。
	* 單元測試（可測試性），並可模組化。


## 比較.NET Core / ASP.NET Code / ASP.NET Core MVC

| 比較 | .NET Core | ASP.NET Code | ASP.NET Core MVC |
| :-: | :-: | :-: | :-: |
|代表|.NET Framework | ASP.NET | ASP.NET MVC|
|說明|平台框架，最廣泛的技術意義|網頁技術|ASP.NET Core 網頁技術中的MVC開發框架|

## MVC

是一種設計的樣式，代表Model、View、Controller 三個部分


* Model 負責處理邏輯及資料面 (資料庫存取)
* View負責處理UI介面（HTML、Javescript、CSS）
* Controller負責接收Request請求、協調Model、View、將回應結果給使用者
 
該分工好處是可以達到關注點分離（SoC）、較好的分層架構、降低複雜度，往後方便對程式進行維護。

## ASP.NET Core MVC?

是支援MVC設計的框架。

## MVC 執行流程

1. 使用者在瀏覽器輸入URL網址後，會發出Request 請求至伺服器
2. 中間會先經過Routing路由機制，找到對應的Controller及Action Method
3. Action 會呼叫Model，以讀取或是更新資料
4. Model會先進行邏輯計算及資料庫存取，再回傳給Action
5. Action會將Model資料丟給View作網頁呈現
6. View Engine 將最終HTML 結果寫入Reponse 輸出資料流，回應給使用者瀏覽器。

## MVC 專案資料夾功能說明

* Properties>launchSettings.json
(本機開發電腦的環境組態檔)
* wwwroot 資料夾 (公開的靜態資源檔目錄，如css、js、images等等)
* Controller 資料夾 (Controller 控制項類別所在目錄)
* Model 資料夾 (Model 模型類別所在目錄)
* View 資料夾 (有Home、Shared、_ViewImports.cshtml、_ViewStart.cshtml)
Home 目錄內有Index、Provacy兩個cshtml
Shared 目錄內有_Layout.cshtml、_ValidationScriptsPartial.cshtml、Error.cshtml
* appsettings.json (供應用程式使用的組態設定)
* Program.cs (程式進入點，主要負責創建Host、環境及組態設定)
* Startup.cs (負責DI Container及Middleware 元件設定)
