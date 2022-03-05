---
weight: 4
title: "面試官常問的 MySQL 資料型態差別 char varchar text"
date: 2022-03-04T17:44:00+08:00
lastmod: 2022-03-05T13:55:33+47:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["資料型態"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

每當我們在建立資料表時，都必須先決定我們要用的資料型態，然而在輸入文字存入資料時，你都用哪一種資料型態呢？

## 文字資料型態

主要有這三點，那我會分別來介紹，最後附上一個表格來整理一下今天所講的。

* char
* varchar
* text

### char 

```char``` 的長度是不可變動的，每一條資料佔用等長的字節空間，適合用在身分證號碼、手機號碼等。

### varchar

```varchar``` 的長度是可以變動的，可以設定最大長度，就適合用於在長度可變的屬性。

### text

```text``` 的長度不需要設定，當我們不知道屬性的長度時，就適合用 ```text```。

### 可變跟不可變的差異

疑~ 什麼是可變，什麼是不可變長度呀 ?

簡單來說，我們現在定義一個 ```char[10]``` 以及 ```varchar[10]```，現在我們存入的資料是 "pinyi" 5字元的字串，

由於 ```char``` 是不可變長度，所佔的長度依舊是 10 ，那如果是 ```varchar``` 可變長度，除了字元所佔的5格，後面的 5 個空格會自動消失，varchar 會自動把長度變成 5 。

取資料時，```char``` 型別要用 ```trim()``` 來去除多餘的空白，而 ```varchar``` 不需要。 


### 查詢速度比較

如果我們來依照查詢速度來比較，想也不用想，一定是 ```char``` 第一名，因為他有固定的數量，比起 ```varchar``` 第二名可變長度還可以更快進行查詢，當然沒有長度限制的 ```text``` 一定是最慢。

### 儲存方式

```char``` 的儲存方式是，對於英文字元 (ASCII) 佔用 1 位元組，對於中文佔用 2 位元組 ; 

```varchar``` 的儲存方式是，對每個英文字元 (ASCII) 佔用 2 位元組，中文也佔用 2 位元組 ; 


### 比較差異表格

| 字串資料型態 | char | varchar | text | 
| :---: | :---: | :---: | :---: |
| 長度狀態 | 不可變 | 可變 | 不需要設定 | 
| 查詢速度 | 第一 | 第二 | 第三 |
| 英文所佔用位元組 | 1 Byte | 2 Byte | Ｘ  |
| 中文所佔用位元組 | 2 Byte | 2 Byte | Ｘ |
| 最大長度 | 1 <= M <= 255 | L <= M or 1 <= M <= 255 | L <2^16 |



## 參考資料

[面試官：你們資料庫選擇varchar和char的原則到底什麼？](https://kknews.cc/code/ey26lgy.html)

[SQL Server 資料型態 char varchar nchar nvarchar](https://ithelp.ithome.com.tw/articles/10213922)

[MySQL性能優化之char、varchar、text的區別](https://www.twblogs.net/a/5c126982bd9eee5e40bb4de6)