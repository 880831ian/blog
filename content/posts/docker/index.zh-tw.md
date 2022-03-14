---
weight: 4
title: "Docker 介紹 (使用 Docker-compose 打包 php+mysql+nginx 環境)"
date: 2022-03-14T17:23:11+08:00
lastmod: 2022-03-14T17:23:11+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["Docker" , "容器" , "虛擬化"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

## 什麼是 Docker ?

Docker 是一種軟體平台，它可以快速建立、測試和部署應用程式。為什麼可以快速建立呢？因為 Docker 會將軟體封裝到『容器』的標準單位。其中包含了程式庫、系統工具、程式碼、執行軟體所需的所有項目。
剛剛有提到容器，也就是 Container ，是一種虛擬化技術，它高效率虛擬化及易於遷移和擴展的特性，非常適合現代雲端的開發及佈署。那 Container 與傳統虛擬機有什麼差別呢？我們來看看下面這張圖

<br>

{{< image src="/images/docker/container-vm.png"  width="700" caption="Container 與 VM 的差異" src_s="/images/docker/container-vm.png" src_l="/images/docker/container-vm.png" >}}

可以看到 Container 是以應用程式為單位，而 VM 則是以作業系統為單位。Container 是一個封裝了相依性資源與應用程式的執行換行 ; VM 則是一個配置好 CPU、RAM 與 Storage 的作業系統，為了更好的做區別，我把 Container、VM 兩個差別用表格說明～

| 比較 | Container | VM |
| :---: | :---: | :---: |
| 單位 | 應用程式 | 作業系統 |
| 適用服務 | 多使用於微服務 | 使用較大型的服務 |
| 硬體資源 | 是以程式為單位，需要的硬體資源很少 | VM 會先佔用 CPU、RAM 等等硬體資源，不管有沒有使用都會先佔用 |
| 造成衝突 | Container 間是彼此隔離的，因此在同一台機器可以執行不同版本的服務 | 會因為版本不同造成環境衝突 |
| 系統支援量 | 單機支援上千個容器 | 一般都幾十個 | 
| 優點 | 1 . Image 較小，通常都幾MB<br>2 . 啟動速度快，通常幾秒就可以生成一個 Container<br>3 . 更新較為容易，只需要利用新的 Image 重新啟動就會更新了 |  1 . 因為硬體層以上都虛擬化，因此安全性相對較高<br>2 . 系統選擇較多，在 VM 可以選擇不同的OS<br>3 . 不需要降低應用程式內服務的耦合性，不需要將程式內的服務個別拆開來部署 |
| 缺點 | 1 . 安全性較 VM 差，因為環境與硬體都與本機共用<br>2 . 在同一台機器中，每一個 Container 的 OS 都是相同的，無法一個為 Windows、一個為 Linux，還是依賴 Host OS<br>3 . Container 通常會切成微服物的方式作部署，在各元件中的網路連結會比較複雜 | 1 . Image 的大小通常 GB 以上，比 Container 大很多<br>2 . 啟動速度通常要花幾分鐘，因此服務重啟速度較慢<br>3 . 資源使用較多，因為不只程式本身，還要將一部分資源分給 VM 的作業系統 |

**總結**

* 更快速的交付和部署：對於開發和維運人員來說，最希望就是一次建立或設定，可以再任意地方正常運行。開發者可以使用一個標準的映像檔來建立一套開發容器，開發完成之後，維運人員可以直接使用這個容器來部署程式碼。Docker 容器很輕很快！容器的啟動時間是秒級的，大量地節約開發、測試、部署的時間。

* 更有效率的虛擬化：Docker 容器的執行不需要額外的虛擬化支援，它是核心層級的虛擬化，因此可以實作更高的效能和效率。

* 更輕鬆的遷移和擴展：Docker 容器幾乎可以在任意的平台上執行，包括實體機器、虛擬機、公有雲、私有雲、個人電腦、伺服器等。 這種兼容性可以讓使用者把一個應用程式從一個平台直接遷移到另外一個。

* 更簡單的管理：使用 Docker，只需要小小的修改，就可以替代以往大量的更新工作。所有的修改都以增量的方式被分發和更新，從而實作自動化並且有效率的管理。

<br>

## 參考資料
[Docker 官網](https://docs.docker.com/get-started/)

[什麼是 Docker？](https://aws.amazon.com/tw/docker/)

[Docker－－從入門到實踐](https://philipzheng.gitbook.io/docker_practice/#yuan-chu-chu-ji-can-kao-zi-liao)
