---
weight: 4
title: "清除 Linux 機器上的 Swap  (Buff、Cache、Swap 比較)"
date: 2022-06-06T10:48:00+08:00
lastmod: 2022-06-06T10:48:00+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["Linux", "Swap", "Cache", "Buff", "實作", "介紹"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

今天在工作時，遇到機器的 Swap 超過預警值，需要手動去清除 Swap，那剛好就由這次機會來介紹要如何清除 Linux 機器上的 Swap，以及查詢 Linux 記憶體的使用狀況！

<br>

## 如何查詢 Linux 記憶體

在講 Swap 之前，我們先來說一下怎麼查詢 Linux 記憶體，可以使用以下指令來顯示：

```
free 
```

<br>

下完後，格式會長這樣：

```sh
              total        used        free      shared  buff/cache   available
Mem:       32765564     8499252     1825132     1857720    22441180    19693100
Swap:      16776188           0    16776188
```

<br>

但這樣子不是很好觀察，所以我們可以加上 `-h` 來顯示大小的單位，讓我更清楚的知道每一個的大小：

```sh
              total        used        free      shared  buff/cache   available
Mem:            31G        8.1G        1.6G        1.8G         21G         18G
Swap:           15G          0B         15G
```

<br>

那我們接著先來說說使用 `free` 查詢後，所有欄位的意思吧！

### free 第一列

* Mem：記憶體的使用資訊
* Swap：交換空間的使用資訊

<br>

### free 第一行

* total：系統總共的可用實體記憶體大小
* used：已被使用的實體記憶體大小
* free：還剩下多少可用的實體記憶體
* shared：被共享使用的實體記憶體大小
* buff/cache：被 buffer 和 cache 使用的實體記憶體大小
* available：可被 **應用程式** 使用的實體記憶體大小

<br>

## buff 跟 cache 以及 Swap 的比較

| 比較 | buff | cache | Swap | 
| :---: | :---: | :---: | :---: |
| 功用 |  記憶體**寫完資料**會先暫存起來，等之後再定期將資料存到硬碟上 | 記憶體**讀完資料**後暫存起來，可以在下此查詢時快速的顯示  | 硬碟的交換分區，當 buff/cache 記憶體已經用完後，又有新的讀寫請求時，就會將部份內存的資料存入硬碟，也就是把內存的部分空間當成虛擬的記憶體來做使用 |

<br>

## free 與 available 的比較

* free：是真正尚未被使用的實體記憶體數量
* available：是**應用程式**認為可用的記憶體數量，可以理解成 available = free + buff/cache

<br>

接下來我們就要進入主題，如何清除 Swap ：

## 清除 Swap

首先第一步我們先使用 `free` 來查看目前的 Swap 使用狀態：

<br>

{{< image src="/images/linux-clear-swap/1.png"  width="800" caption="Swap 使用狀態" src_s="/images/linux-clear-swap/1.png" src_l="/images/linux-clear-swap/1.png" >}}
可以看到我們的 Swap used 是 797M，那我們設定他不能超過 5%，超過就會通知，所以我們接著要把它手動清除。
<br>




<br>

## 參考資料

[Linux內存、Swap、Cache、Buffer詳細解析](https://os.51cto.com/article/636622.html)
[linux free 命令下free/available區別](https://www.796t.com/content/1545715382.html)