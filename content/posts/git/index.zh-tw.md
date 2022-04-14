---
weight: 4
title: "Git 介紹"
date: 2022-02-10T16:15:00+08:00
lastmod: 2022-04-14T16:15:00+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["Git", "操作指令", "介紹"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

## 什麼是 Git ?

不管是不是工程師，只要常常需要使用電腦工作，每天一定都會新增、修改、刪除許多檔案，我們看到這張圖：

<br>

{{< image src="/images/git/sample.png"  width="450" caption="很多人的電腦裡面都有這樣的內容" src_s="/images/git/sample.png" src_l="/images/git/sample.png" >}}

<br>

這張圖是一個菜鳥工程師在整理檔案時的方法，因為每一天都會對這份檔案做不同的處理，但為了保留以前的版本，所以也不會刪除舊的檔案，只好用日期或是版本來做分類，時間越久，檔案就累積越多，假如不小心刪除，也找不回來紀錄，也不清楚不同檔案的差異，所以有了Git這項工具。

<br>

* Git 為分散式版本控制系統，是為了更好管理Linux內核而開發的。
* Git 的優點：免費開源、速度快、檔案體積小、分散式系統。
* Git 的缺點：指令繁雜，但可以透過GUI工具解決。
* Git 會紀錄哪些資料：更動前 vs 更動後的程式碼、修改者、修改時間、修改原因（修改者需要自行撰寫 commit message）。

<br>

我們也常常聽到 GitHub or GitLab 那跟 Git 是一樣的東西嗎？

Ans：GitHub(GitLab) 是基於Web的平台，結合了Git的版本控制功能，為開發團隊提供了儲存、分享、發布和合作開發項目的中心化雲存儲的場所。

<br>

## Git 操作指令

Git 的操作指令繁多，包含環境類、查看類、提交類、分支類、遠端類、合併類、還原類等等，所以才有了 Git GUI 工具，筆者很推薦 [Gitkraken](https://www.gitkraken.com/)，雖然需要付費，但真的很方便，畫面也很乾淨簡潔。如果是學生的話，還可以使用 [GitHub Student Developer Pack](https://education.github.com/pack) 免費使用歐！

<br>

{{< image src="/images/git/gitkraken.png"  width="500" caption="Git GUI 工具 (Gitkraken)" src_s="/images/git/gitkraken.png" src_l="/images/git/gitkraken.png" >}}

<br>


接下來我們會依照環境類、查看類、提交類、分支類、遠端類、合併類、還原類依序下去介紹～

### 環境類

使用每一個程式或工具，必須先把它安裝到自己電腦上對吧！但因為大家使用的系統都不一樣，所以這邊就不列出要怎麼進行安裝，可以參考 [Git 安裝教學](https://git-scm.com/book/zh-tw/v2/%E9%96%8B%E5%A7%8B-Git-%E5%AE%89%E8%A3%9D%E6%95%99%E5%AD%B8)

當我們安裝好後，我們就可以一起進入 Git 的世界囉！

<br>

#### init

首先，找一個你要開始進行版本控制 (Git) 的資料夾， 使用：

```sh
$ git init
```
要記得要到版本控制的資料夾目錄下才使用這個指令歐！

<br>

使用完後，會看到跑出下面這些文字：

<br>

{{< image src="/images/git/init.png"  width="700" caption="Git init" src_s="/images/git/init.png" src_l="/images/git/init.png" >}}

<br>

此外資料夾內也會多一個隱藏檔案 `.git` ，他是用來存放 git 的紀錄，所以不要亂刪除歐：

```sh
$ tree -a -d
.
└── .git
    ├── hooks
    ├── info
    ├── objects
    │   ├── info
    │   └── pack
    └── refs
        ├── heads
        └── tags
```        
<br>

回到剛剛圖片，它說明默認會使用 master 這個來作為初始分支，並記得要使用 `git config` 來做設定，那 `git config` 是要做什麼用的呢！？

#### config

在推送Commit的時候，會顯示使用者名稱以及電子郵件，所以要先在推送前設定好，這時就使用：

```
$ git config --global user.name "your name" 
$ git config --global user.email "your email"
```
分別設定使用者名稱以及電子郵件，這樣共同使用版本控制的人，才分的出來誰是誰！ (若只要在單個專案下設定使用者名稱及電子郵件，就不需要設定 --global 參數)

<br>

### 查看類

我們剛剛已經設定好 config ，如果想查看檔案的git狀態，就使用：

```sh
$ git status 
```
指令，可以查看現在資料夾內有哪些檔案還沒加入版本控制，或是已經加入但還沒Commit成新版本。

<br>

{{< image src="/images/git/status.png"  width="700" caption="Git status" src_s="/images/git/status.png" src_l="/images/git/status.png" >}}

<br>



<br>

## 參考資料

[什麼是 Git？為什麼要學習它？](https://gitbook.tw/chapters/introduction/what-is-git)

[Git：基本概念介紹與指令](https://medium.com/@tina2793778/git-%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5%E4%BB%8B%E7%B4%B9%E8%88%87%E6%8C%87%E4%BB%A4-d5d85607cd7d)

[[Git] 初始設定](https://ithelp.ithome.com.tw/articles/10240965)

[連猴子都能懂的Git入門指南](https://backlog.com/git-tutorial/tw/)

[Git教學】分支合併: merge 與 rebase 差異](https://www.maxlist.xyz/2020/05/02/git-merge-rebase/)