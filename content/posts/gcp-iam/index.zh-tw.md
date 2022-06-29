---
weight: 4
title: "Google Cloud Platform (GCP) 百科全書  - IAM 與管理 [ EP.1 ]"
date: 2022-06-29T10:00:00+08:00
lastmod: 2022-06-29T10:00:00+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["GCP", "GKE", "K8s"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

本篇是我們進入 GCP 的第一篇文章，詳細的文章列表大家可以到[這一篇查看](https://pin-yi.me/gcp-introduce/) ～ IAM 的全名是 Identity and Access Management，當我們藉由 IAM，可以授與特定 Google Cloud 資訊的 **精細** 訪問權限，並防止對其他資源的訪問。疑！？為什麼是精細？我們接著看下去，我們可以採用最小權限安全原則，該原則要求任何人都不應擁有超出時間所需數量的權限。

<br>

## IAM 的工作原理

首先我們先來了解一下 IAM 的工作原理，藉由 IAM，我們可以定義誰 (哪一個身份) 對哪些資源去有哪種的訪問權限 (角色) 來管理訪問權限控制。什麼是資源？例如， Compute Engine 虛擬機實例、Google Kubernetes Engine (GKE) 集群和Cloud Storage 存儲分區都是Google Cloud 資源，我們用於整理資源的組織或資料夾、項目等也都是資源

<br>

{{< image src="/images/gcp-iam/logo.png"  width="250" caption="GCP IAM Logo" src_s="/images/gcp-iam/logo.png" src_l="/images/gcp-iam/logo.png" >}}

<br>

我們可以把它理解成 

{{< admonition type=quote title="引用" >}}
什麼 『 人 』，可以對什麼『 資源 』，做什麼『 事情 』
{{< /admonition >}}

<br>

IAM 不會直接向用戶授與資源的訪問權限，而是將權限分成多個角色，然後將這些角色授與經過身份驗證的主帳號。(以前 IAM 會將主帳號稱為成員，目前部分 API 仍然使用此術語。)

<br>

{{< image src="/images/gcp-iam/iam-overview-basics.png"  width="800" caption="IAM 中的權限管理" src_s="/images/gcp-iam/iam-overview-basics.png" src_l="/images/gcp-iam/iam-overview-basics.png" >}}

<br>

可以看到這張圖片，訪問權限管理主要包含三個部分：
* 主帳號 (Principal)：主帳號可以是 Google 帳號 (針對用戶)、服務帳號 (針對應用和計算工作負載)、Google 群組或Google Workspace 帳號或可以訪問資源的Cloud Identity 網域等等。
* 角色 (Role)：一個角色對應一組權限，權限決定了可以對資源執行的操作。向主帳號授與某個腳色， 代表授與該角色包含的所有權限給主帳號。
* 政策 (Policy)：允許政策 (Allow Policy) 是將一個或多個主體綁定在各個角色，當想要定義誰 


<br>

## 參考資料

[IAM 概覽](https://cloud.google.com/iam/docs/overview)

[什麼是 Cloud IAM？GCP 權限管理服務介紹](https://blog.cloud-ace.tw/identity-security/what-is-cloud-iam/)