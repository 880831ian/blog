---
weight: 4
title: "Google Cloud Platform (GCP) 百科全書  - Cloud Source Repositories
 [ EP.3 ]"
date: 2022-07-01T17:02:00+08:00
lastmod: 2022-07-01T17:02:00+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["GCP", "Source Repositories", "git"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

本篇是我們進入 GCP 的第三篇文章，詳細的文章列表大家可以到[這一篇查看](https://pin-yi.me/gcp-introduce/) ～ 跟大家介紹一下今天的主題 Cloud Source Repositories，聽到 Source Repositories 是不是感覺跟什麼東西很像呀，沒錯，就跟我們的 GitHub or GitLab 一樣，可以用來存放我們的程式碼的儲存庫，我們來看看官方怎麼介紹他吧：

<br>

{{< image src="/images/gcp-gcsr/1.png"  width="800" caption="官方介紹 Cloud Source Repositories" src_s="/images/gcp-gcsr/1.png" src_l="/images/gcp-gcsr/1.png" >}}

<br>		

很好歐，非常簡單明瞭 🤣，沒錯，Cloud Source Repositories 就是託管在 Google Cloud 上功能齊全(？)的私有 Git 儲存庫。為什麼會打一個問號呢？是因為他其實沒有那麼好用，所以我們通常的做法，還是會依靠 GitHab 或是 GitLab 來存放程式碼，再透過鏡像 (mirror) 的方式到 Google Cloud Source Repositories。 那我們就開始囉～

<br>

## Cloud Source Repositories 測試

### 建立 GitLab Project

首先，我們用 GitLab 來當示範，如何透過鏡像 (mirror) 到 Cloud Source Repositories 上面，我們先在 GitLab 上建立一個 Project：

<br>

{{< image src="/images/gcp-gcsr/2.png"  width="800" caption="建立  GitLab Project" src_s="/images/gcp-gcsr/2.png" src_l="/images/gcp-gcsr/2.png" >}}

<br>		
	
### 使用 gcloud 指令建立 Source Repo

1. 首先，一定要先裝 `gcloud` 指令到本機，這個步驟，前面文章也有說過，這邊就不在說明，我們先使用一下指令來查看目前所在的 GCP 專案：

```
gcloud config get-value project
```

<br>

正常來說，如果有先用 config 設定好，會直接跳出你目前的專案 ID，如果沒有跳出來，請使用下面指令來設定：

```
gcloud config set project <project id>
```

<br>

2. 接著我們要啟動該專案的 Cloud Source Repositories API：

```
gcloud services enable sourcerepo.googleapis.com
```

<br>

3. 創建 Cloud Source Repositories

```
gcloud source repos create <repo name>
```

4. 完成後，開啟 GCP 檢查一下是否有建立成功～點擊左側 menu > **Source Repositories**，

<br>

{{< image src="/images/gcp-gcsr/3.png"  width="800" caption="開啟 Source Repositories" src_s="/images/gcp-gcsr/3.png" src_l="/images/gcp-gcsr/3.png" >}}

<br>	

{{< image src="/images/gcp-gcsr/4.png"  width="1000" caption="成功建立 Source Repositories" src_s="/images/gcp-gcsr/4.png" src_l="/images/gcp-gcsr/4.png" >}}

<br>

## 將程式碼新增至存放區中

1. 我們要在這一步來設定鏡像 (mirror)，首先我們看剛剛上面建立好的 Source Repositories，其中有一個**手動產生的憑證**，點選 **產生及儲存 Git 憑證**
	
<br>	

{{< image src="/images/gcp-gcsr/5.png"  width="650" caption="產生及儲存 Git 憑證" src_s="/images/gcp-gcsr/5.png" src_l="/images/gcp-gcsr/5.png" >}}

<br>	

2. 點完後會需要先登入你的 GCP 帳號，登入完後會出現以下內容：

<br>	

{{< image src="/images/gcp-gcsr/6.png"  width="800" caption="Configure Git" src_s="/images/gcp-gcsr/6.png" src_l="/images/gcp-gcsr/6.png" >}}

<br>

3. 接著把藍色框框內的輸入到終端機內

<br>

## 參考資料

[Cloud Source Repositories documentation](https://cloud.google.com/source-repositories/docs)

[Mirroring GitLab repositories to Cloud Source Repositories](https://cloud.google.com/architecture/mirroring-gitlab-repositories-to-cloud-source-repositories)