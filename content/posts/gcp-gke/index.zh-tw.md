---
weight: 4
title: "Google Cloud Platform (GCP) 百科全書  - Kubernetes Engine [ EP.6 ]"
date: 2022-07-07T16:57:00+08:00
lastmod: 2022-07-07T16:57:00+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"


tags: ["GCP" ,"GKE" , "Cloud Build" ,"Source Repositories", "Container Registry" ]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

本篇是我們進入 GCP 的第六篇文章，詳細的文章列表大家可以到[這一篇查看](https://pin-yi.me/gcp-introduce/) ～ 跟大家介紹一下今天的主題 Kubernetes Engine，Kubernetes Engine 也就是將 kubernetes 移到 GCP 上面的一個服務，可以輕鬆的自動部署、伸縮和管理 Kubernetes。詳細的 kubernetes 介紹可以參考 [Kubernetes (K8s) 介紹 - 基本](https://pin-yi.me/k8s/)。

<br>

我們今天文章，也跟上一篇一樣，會使用到之前我們所介紹到的每一個服務，我們一樣會透過 GitLab Mirror 到 Cloud Source Repo，再透過觸發 Cloud Build 來創建 image 到 Container Registry 最後部署至 Google Kubernetes Engine (GKE) 上。

<br>

{{< image src="/images/gcp-gke/1.png"  width="1200" caption="GKE 流程圖" src_s="/images/gcp-gke/1.png" src_l="/images/gcp-gke/1.png" >}}

<br>		




<br>

## 參考資料


[Google Kubernetes Engine](https://cloud.google.com/kubernetes-engine)
