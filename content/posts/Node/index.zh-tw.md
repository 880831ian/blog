---
weight: 4
title: "Node.js 介紹"
date: 2022-03-30T10:45:29+08:00
lastmod: 2022-03-30T10:45:29+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["Node.js", "實作", "介紹"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

## 什麼是 Node.js ?

Node.js 是能夠在伺服器端運行 JavaScript 的開放原始碼、跨平台執行環境。

Node.js 採用 Google 開發的 Chrome V8 JavaScript 引擎和 libuv，可以用指令去執行 JavaScript，使用非阻塞輸入輸出、非同步、事件驅動等技術來提高效能，可最佳化應用程式的傳輸量和規模。這些技術通常用於資料密集的即時應用程式。

<br>

{{< image src="/images/node/main-nodejs.png"  width="700" caption="主要的 Node.js 組件 ([An Intro to Node.js That You May Have Missed](https://itnext.io/an-intro-to-node-js-that-you-may-have-missed-b175ef4277f7))" src_s="/images/node/main-nodejs.png" src_l="/images/node/main-nodejs.png" >}}

<br>

以下會先從非阻塞輸入輸出、非同步，再談到事件驅動。

<br>

### 非阻塞

輸入輸出(I/O) 是程式跟系統記憶體或網路的互動，例如發送 HTTP 請求、對資料庫CRUD 操作等等。

以網站開發者角度來看，大部分的網站程式都不需要太多的 CPU 計算，反而是在等待大量的 I/O 處理完畢 (HTTP 請求、資料庫的取得資料或是更新資料等)，所以處理 I/O 的速度會是網頁程式效能的關鍵。

<br>

那要怎麼才能讓等待 I/O 的時間，不要卡住後續的程式碼呢？可以讓程式一邊等 I/O 處理，一邊繼續執行其他部分的程式碼，主要有兩種方法：

1. 多執行緒 (multi-threaded)：使用阻塞 (blocking) I/O 的設計。
2. 單執行緒 (single-threaded)：使用非阻塞 (non-blocking) I/O 的設計 + 非同步 (asynchronous) 處理。

<br>

**阻塞就是 I/O 的處理阻擋了其他後續程式碼的執行**。舉個例子：

| 阻塞 (blocking) | 非阻塞 (non-blocking) | 
| :---: | :---: |
| 阻塞後續程式碼的執行，就好像是我們去附近買烤肉，交給老闆後，為了想吃到熱騰騰的食物，所以只能留在原地，不能先去其他地方 | 非阻塞不會阻擋後續程式碼的執行，就好像是我們去百貨公司美食街點餐，點完餐後會拿到一個呼叫器，就可以先離開，等到呼叫器想了再回去拿即可 |

<br>

像是 **Python、Ruby 等語言是使用多執行緒 (multi-threaded)，使用阻塞 I/O** ：

程式會等網路或是記憶體得作業結束後才會繼續往下，等待時間這個作業中的執行緒不會去做其他事情。

如果想要達到『等待 I/O 期間不要卡住其他程式碼』，的做法就是新開一個執行緒，直到任務完成，再告訴主執行緒說( 我完成囉！) 即可。

<br>

**Node.js 使用單執行緒 (single-threaded) ，非阻塞 I/O + 非同步函式**：

Node.js 使用非阻塞設計，那要怎麼去操作資料庫或是 HTTP 請求的輸入輸出呢？就要透過非同步 (asynchronous) 來處理囉！

<br>

###  非同步

非同步也可以稱為異步，它的作用就是讓程式不要被阻擋等 I/O 處理完，才可以跑下一行程式碼，函式中的 callback 被呼叫的時候再執行後續要做的事情。

JaveScript 實現非同步的方法不斷演進著：從 callback、promises 到最新的 asymc-await 函式。

<br>

同步 vs. 非同步

我們使用 Node.js 的 File System (文件系統)模組作為簡單的例子，以下是 **同步的寫法** (程式會停止前進，直到跑完 `readFileSync`)：

```js
const fs = require("fs");
const data = fs.readFileSync("file.md", { encoding: "utf8" }); // blocks here until file is read
console.log(data);
```

```sh
$ node index.js
readFileSync 測試
```

<br>

以下是非同步的寫法 (程式會繼續從下處裡後續程式碼，等 `readFile` 跑完再回來對資料 `doSomething`)：

```js
const fs = require("fs");
fs.readFile("file.md", { encoding: "utf8", flag: "r" }, (err, data) => {
  if (err) throw err;
  doSomething(data);
});
function doSomething(data) {
  console.log(data);
}
```

```sh
$ node index.js
readFileSync 測試
```

<br>

**為什麼 Node.js 有了非阻塞 I/O 跟 非同步函式，就可以實現單執行緒設定呢?** 

雖然 Google V8 JavaScript 引擎是單執行緒設定，但它的底層 C ++ API 並不是。這代表我們可以呼叫一些非同步的程式碼像是 File System 時，Node 會請它底層的 `Libuv` 去跑這些程式碼，跑完後再執行 callback 或拋出錯誤。

<br>

{{< image src="/images/node/run-nodejs.png"  width="700" caption="Node.js 運行時的圖示 ([An Intro to Node.js That You May Have Missed](https://itnext.io/an-intro-to-node-js-that-you-may-have-missed-b175ef4277f7))" src_s="/images/node/run-nodejs.png" src_l="/images/node/run-nodejs.png" >}}

<br>

Libuv 是非同步處理的函式庫（以 C 語言為主)，在面對非同步作業時，會開啟執行緒池 (thread pool / worker pool) ，預設共有四個執行緒 (也就是 worker threads)，運算這些 I/O 用的方式是事件迴圈 (event loop)。

所以就算 V8 處理程式碼是單執行緒，一但進入到 Libuv 手上，它還是會幫你把會阻塞的 I/O 分工到執行緒池中交給不同的執行緒去處理，直到 callback 發生才會丟回應讓程式知道。


那我們來看看 Libuv 如何透過事件迴圈有效的處理非同步 I/O。

<br>

### 事件驅動

#### 事件 (event)

事件是指用戶或是系統作出的動作，例如使用者點選按鈕，或是檔案讀取完成、某種錯誤產生等等，都叫做事件。

<br>

#### 事件驅動 (event-driven)

事件驅動是一種程式執行模型，表示程式的進行是依據事件的發生而定，監聽到事件就處理、處理完就執行 callback ，透過不斷的監聽跟回應事件執行程式。

而事件驅動在不同的地方有不同的實現。瀏覽器 (前端) 和 Node.js (後端) 基於不同的技術實現了各自的事件迴圈。就 Ndoe.js 來說，事件就是交給 Libuv 去處理 ; 至於瀏覽器的事件迴圈在 HTML 5 的規範中有定義。

<br>

#### 事件迴圈

因為 Node.js 只有一個執行緒，所以當 Libuv 把非同步事件處理完後，callback 要被丟回應用程式中排隊，等待主執行緒的 stack 為空的時候，才會開始執行。這個排隊的地方就是事件佇列 (event queue)。

Libuv 會不斷檢查有沒有 callback 需要被執行，有的話分配到主執行緒結束手邊的程式後處理，因此這個過程稱為 『事件迴圈』。

<br>

{{< image src="/images/node/event-loop.png"  width="700" caption="事件迴圈(event loop) ([Node.js 101 — 單執行緒、非同步、非阻塞 I/O 與事件迴圈](https://medium.com/wenchin-rolls-around/node-js-101-%E5%96%AE%E5%9F%B7%E8%A1%8C%E7%B7%92-%E9%9D%9E%E5%90%8C%E6%AD%A5-%E9%9D%9E%E9%98%BB%E5%A1%9E-i-o-%E8%88%87%E4%BA%8B%E4%BB%B6%E8%BF%B4%E5%9C%88-ef94f8359eee))" src_s="/images/node/event-loop.png" src_l="/images/node/event-loop.png" >}}

<br>

Libuv 事件迴圈有哪些階段呢？

{{< image src="/images/node/libuv.png"  width="700" caption="libuv 事件迴圈 ([nexocode](https://nexocode.com/blog/posts/behind-nodejs-event-loop/))" src_s="/images/node/libuv.png" src_l="/images/node/libuv.png" >}}

<br>

Libuv 的事件迴圈共有六個階段，每個階段的作用如下：

1. Timers：執行 setTimeout() 和 setInterval() 中到期的 callback，函式裡的時間值是個門檻 (也就是如果設定 1000，最快可以一秒後馬上執行，但可能超過一秒才會執行)，這還要看 OS 如何排序所有的 callback。
2. Pending callbacks：上一輪迴圈中有少數的 I/O callback 會被延遲到這一輪的這一階段執行。
3. Idle, prepare：Idle handle callback 會被執行、prepare handles 會在迴圈被 I/O 阻塞前執行 callback。
4. Poll：最重要的一個階段，它會尋找新的 I/O 事件，可能的話會馬上執行 I/O callback，如果沒辦法馬上執行，它會延遲執行並把它註冊為一個 pending callback 在下一輪執行。這個階段有兩個主要任務：計算要阻塞多久、還有找遍所有在 polling 佇列中事件並執行 callback，直到佇列清空或是執行的 callback 數量達到系統上限。這個階段會阻塞執行緒。
5. Check：跟 prepare 相反，會在迴圈被 I/O 阻塞後執行 callback，還有執行 setImmediate() 的 callback。
6. Close callbacks：執行 close 事件的 callback，利如 socket.destroy()。

<br>

事件迴圈就是不斷重複以上階段。每個階段都有自己的 callback 佇列，在進入某個階段時，都會從所屬的佇列中取出 callback 來執行，當佇列為空或者被執行 callback 的數量達到系統的最大數量時，就會進入下一階段。

<br>

根據以上提到事件驅動、單執行緒和非同步、非阻塞的 I/O 處理特性，Node.js 很適合拿來開發 I/O 密集型應用程式，如影音串流、即時互動、在線聊天、遊戲、協作工具、股票行情等軟體。

<br>

## NPM

NPM 是跟 Node.js 一起安裝的線上套件庫，可以下載各式各樣的 JavaScript 套件來使用，能解決 Node.js 代碼部署上的很多問題。

<br>

安裝好後，可以使用 `npm -v` 來檢查版本：

```sh
$ npm -v
8.5.0
```
<br>

跟 NPM 息息相關的是 `package.json` 這個檔案，他是掌管專案資訊的重要檔案，我們可以使用 `init` 指令來設定 package.json：

```sh
$ npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.
.... 省略 ....

Press ^C at any time to quit.
package name: (message) demo
version: (1.0.0) 
description: 
entry point: (index.js) 
test command: 
git repository: 
keywords: 
author: ian <880831ian@gmail.com>
license: (ISC) 
```
* name: 就是該專案的名字，它預設就是該目錄名。
* description: 專案描述。
* entry point: 專案切入點，這有點複雜，之後再說。
* test command: 專案測試指令，之後說。
* git repository: 專案原始碼的版本控管位置。
* keywoard: 專案關鍵字
* author: 專案作者，以 author-name <author@email.com> 寫之。
* license: 專案版權。

<br>

設定好後，專案資料夾就會多一個 `package.json` 的檔案，打開後可以看到，是我們剛剛所設定好的資訊：

```json
{
  "name": "demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "ian <880831ian@gmail.com>",
  "license": "ISC"
}
```

<br>

接下來，要如何下載網路上的模組，要使用 `install` 指令來下載，我們下載 Node.js 最小又靈活的 Web 應用程式框架 express 來做示範：

```sh
$ npm install express

added 50 packages, and audited 51 packages in 1s

2 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

<br>

下載好後，可以看到剛剛 `package.json` 檔案多了 `dependencies` 欄位，它裡面會紀錄我們安裝了哪些套件，所以未來我們想知道專案使用了哪些套件，我們可以從 `dependencies` 這個欄位知道。 

```json
{
  "name": "demo",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "ian <880831ian@gmail.com>",
  "license": "ISC",
  "dependencies": {
    "express": "^4.17.3"
  }
}
```

<br>

除此之外，還會多一個 `node_modules` 資料夾，這個資料夾就會存放我們所下載的套件

```sh
$ ls node_modules/
accepts             cookie-signature    etag                inherits            mime-types          qs                  setprototypeof
array-flatten       debug               express             ipaddr.js           ms                  range-parser        statuses
body-parser         depd                finalhandler        media-typer         negotiator          raw-body            toidentifier
bytes               destroy             forwarded           merge-descriptors   on-finished         safe-buffer         type-is
content-disposition ee-first            fresh               methods             parseurl            safer-buffer        unpipe
content-type        encodeurl           http-errors         mime                path-to-regexp      send                utils-merge
cookie              escape-html         iconv-lite          mime-db             proxy-addr          serve-static        vary
```

<br>


當我們都安裝好後，express 已經包在 `node_modules` 目錄內，在專案裡面，就可以使用 require('套件名稱‘) 來使用套件囉！
```js
var express = require('express');
```

<br>

因為這些套件都可以直接在網路上下載到，所以在推送 git 專案時，可以使用 `.gitignore` 來隱藏不想被 push 的檔案，當我們想要下載套件回來時，只要使用：

```sh
$ npm install
```
就可以依照 `package.json` 裡面的 `dependencies` 來下載套件！

## REPL 

Node.js REPL  (交互式解釋器)：表示一個電腦環境，類似 Windoes 系統的終端或是 Unix/Linux 的 Shell，我們可以在終端機上輸入命令，並接收系統的響應。

Node 自帶了交互式解釋器，可以執行以下任務：

* 讀取：讀取用戶輸入，解析輸入的 JavaScript 數據結構並儲存在內存中。
* 執行：執行輸入的數據結構。
* 顯示：輸出結果。
* 循環：循環以上任務直到用戶按下兩次的 ctrl+c 按鈕退出。

<br>

我們可以輸入以下命令來啟動 Node 的終端：
```sh
$ node
Welcome to Node.js v16.14.2.
Type ".help" for more information.
>
```

<br>

可以執行簡單的數學運算：

```sh
$ node

Welcome to Node.js v16.14.2.
Type ".help" for more information.
> 1+4
5
> 5/2
2.5
> 3*7
21
> 4-3
1
> 1 + (2*4) -5
4
>
```

<br>

也可以將數據存在變數中，在需要時使用它。變數宣告需要使用 `var` 關鍵字，如果沒有使用關鍵字，會直接顯示出來。也可以使用 `console.log()` 來輸出變數

```sh
$ node

Welcome to Node.js v16.14.2.
Type ".help" for more information.
> x = 19
19
> var y = 10
undefined
> x + y
29
> console.log("Hello")
Hello
undefined
>
```

<br>

Node REPL 也支持輸入多行程式，我們試著寫一個 do-while 迴圈：

```sh
Welcome to Node.js v16.14.2.
Type ".help" for more information.
> var x = 0
undefined
> do {
... x++;
... console.log("x:" + x);
... } while (x<5);
x:1
x:2
x:3
x:4
x:5
undefined
>
```

<br>

也可以使下底線(_)來獲得上一個程式的運算結果：

```sh
$ node

Welcome to Node.js v16.14.2.
Type ".help" for more information.
> var x = 10
undefined
> var y = 20
undefined
> x + y
30
> var sum = _
undefined
> console.log(sum)
30
undefined
>
```


<br>

## 參考資料

[Node.js 官網](https://nodejs.dev/learn/introduction-to-nodejs)

[Node.js 101 — 單執行緒、非同步、非阻塞 I/O 與事件迴圈](https://medium.com/wenchin-rolls-around/node-js-101-%E5%96%AE%E5%9F%B7%E8%A1%8C%E7%B7%92-%E9%9D%9E%E5%90%8C%E6%AD%A5-%E9%9D%9E%E9%98%BB%E5%A1%9E-i-o-%E8%88%87%E4%BA%8B%E4%BB%B6%E8%BF%B4%E5%9C%88-ef94f8359eee)

[An Intro to Node.js That You May Have Missed
](https://itnext.io/an-intro-to-node-js-that-you-may-have-missed-b175ef4277f7)

[Sequelize
](https://sequelize.org/v6/index.html)

[透過 sequelize 來達成 DB Schema Migration
](https://hackmd.io/@TSMI_E7ORNeP8YBbWm-lFA/ryCtaVW_M?print-pdf#%E4%BD%BF%E7%94%A8sequelize%E5%BB%BA%E7%AB%8B%E4%B8%80%E5%BC%B5user-table)