---
weight: 4
title: "什麼是 IaC ? Terraform 又是什麼？"
date: 2022-09-26T15:58:00+08:00
lastmod: 2022-09-26T15:58:00+08:00
draft: false
author: "PinYi"
authorLink: "https://pin-yi.me"
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["IaC", "基礎設施即代碼", "Terraform"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

跟上一篇 Snyk 一樣，本系列也是去參加 DevOpsDay Taipei 2022 活動聽到各位產業大佬目前在使用的名詞以及技術，想說回家也充實一下自己，了解一下在 DevOpsDay 最常出現的 IaC 是什麼？以及聽說很方便的 Terraform 又是什麼，將學習的過程打成此篇筆記，歡迎大家多交流，那我們就開始囉。

目前打算寫本篇介紹 IaC 以及 Terraform 以外，之後還想寫另外兩篇用 Terraform 建立 GCE 以及 GKE。大家可以持續關注此篇文章，最後會附上連結 😍

<br>

## IaC

Iac 全名是  Infrastructure as Code (基礎設施即代碼)，從字面意思就可以略知一二，也就是把基礎設施變成程式碼，在還沒有這些 IaC 工具之前，大家都是土法煉鋼的去機器內，或是開啟 WEB UI 畫面來進行建置或設定，雖然使用 UI 點一點就建好了，但這些步驟都沒有被紀錄下來 (git)，也沒有辦法透過 Review 的方式來避免人為操作錯誤，因為有了 IaC 這些工具，可以將實際的操作流程，轉換成程式碼或是其他像是 JSON、Yaml 的方式給紀錄下來，以下是導入 IaC 帶來的好處：

<br>

1. 建置 CI/CD 自動化 (不需要再仰賴 UI 進行操作)
2. 版本控制 (大家可以一起 Review 避免出現人為錯誤)
3. 重複使用 (可以將常用的參數挖洞，減少重複建置時間)
4. 環境一致性 

<br>

(以上 IaC 說明來自 [小惡魔 - 初探 Infrastructure as Code 工具 Terraform vs Pulumi ](https://blog.wu-boy.com/2021/02/introduction-to-infrastructure-as-code-terraform-vs-pulumi/)文章，寫得真的很好，推推)

<br>

{{< image src="/images/terraform/iac.png"  width="800" caption="Infrastructure as Code [初心企服行研07：认识「基础设施即代码」(Infrastructure as Code) — 初心内参](https://www.36dianping.com/info/15473.html)" src_s="/images/terraform/iac.png" src_l="/images/terraform/iac.png" >}}

<br>

## Terraform 

接著我們就來介紹其中一個 IaC 的工具 - Terraform，Terraform 是什麼呢？根據官網的說明可以知道，Terraform 是 [HashiCorp](https://www.hashicorp.com/) 所開發的基礎設施即代碼工具。它可以使用人類方便讀的配置文件來定義資源和基礎設施，以下有使用 Terraform 幾個優點：

<br>

1. Terraform 可以管理多個雲平台上的基礎架構
2. 使用人類可讀的配置語言來幫助我們快速編寫基礎架構代碼
3. 可以將配置提交給版本控制，以安全地在基礎架構上進行協作

<br>

### 管理任何基礎設施

Terraform 提供插件讓 Terraform 可以通過其 API 與雲平台和其他服務進行交互。HashiCorp 和 Terraform 社區編寫了 1,000 多個提供商來管理

<br>

## 參考資料

[初探 Infrastructure as Code 工具 Terraform vs Pulumi](https://blog.wu-boy.com/2021/02/introduction-to-infrastructure-as-code-terraform-vs-pulumi/)