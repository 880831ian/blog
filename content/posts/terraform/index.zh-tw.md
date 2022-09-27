---
weight: 4
title: "什麼是 IaC ? Terraform 又是什麼？"
date: 2022-09-26T15:58:00+08:00
lastmod: 2022-09-27T17:14:00+08:00
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

## 什麼是 IaC ?

Iac 全名是  Infrastructure as Code (基礎設施即代碼)，從字面意思就可以略知一二，也就是把基礎設施變成程式碼，在還沒有這些 IaC 工具之前，大家都是土法煉鋼的去機器內，或是開啟 WEB UI 畫面來進行建置或設定，雖然使用 UI 點一點就建好了，但這些步驟都沒有被紀錄下來 (git)，也沒有辦法透過其他人一起 Review 的方式來避免人為操作錯誤。因此有了 IaC 這些工具，可以將實際的操作流程，轉換成程式碼或是其他像是 JSON、Yaml 的方式給紀錄下來，以下是導入 IaC 帶來的好處：

<br>

1. 建置 CI/CD 自動化 (不需要再仰賴 UI 進行操作)
2. 版本控制 (大家可以一起 Review 避免出現人為錯誤)
3. 重複使用 (可以將常用的參數挖洞，減少建置時間)
4. 環境一致性 

<br>

(以上 IaC 說明來自 [小惡魔 - 初探 Infrastructure as Code 工具 Terraform vs Pulumi ](https://blog.wu-boy.com/2021/02/introduction-to-infrastructure-as-code-terraform-vs-pulumi/)文章，寫得真的很好，推推)

<br>

{{< image src="/images/terraform/iac.png"  width="800" caption="Infrastructure as Code [初心企服行研07：认识「基础设施即代码」(Infrastructure as Code) — 初心内参](https://www.36dianping.com/info/15473.html)" src_s="/images/terraform/iac.png" src_l="/images/terraform/iac.png" >}}

<br>

## Terraform 又是什麼？ 

IaC 的工具有很多種，接著我們就來介紹其中一個工具 - Terraform，Terraform 是什麼呢？根據官網的說明可以知道，Terraform 是 [HashiCorp](https://www.hashicorp.com/) 所開發的基礎設施即代碼工具。它可以使用人類方便讀的配置文件來定義資源和基礎設施，以下有使用 Terraform 幾個優點：

<br>

1. Terraform 可以管理多個雲平台上的基礎架構
2. 使用人類可讀的配置語言來幫助我們快速編寫基礎架構代碼
3. 可以將配置提交給版本控制，安全地在基礎架構上進行協作

<br>

### 管理任何基礎設施

Terraform 提供插件讓 Terraform 可以通過其 API 與雲平台和其他服務進行交互。HashiCorp 和 Terraform 社區編寫了 2,500 多個提供商來管理像 AWS、Azure、GCP、Kubernetes、Helm、GitHub 等資源，可以到 [Terraform Registry](https://registry.terraform.io/browse/providers?_gl=1*a2tlfj*_ga*MTc0NDA4ODQ5MC4xNjYzOTI0ODI5*_ga_P7S46ZYEKW*MTY2NDE4MjM4NC40LjEuMTY2NDE4Mjc5Ny4wLjAuMA..) 查看更多平台或服務的提供者，當然如果沒有找到想要的提供者，也可以自己編寫自己的套件。

<br>

### 標準化部署工作流程

提供商會將基礎設施的每個單元 (例如建立 VM 或是 VPC) 定義為資源。你可以將來自不同提供者的資源組合，稱為模塊，讓我們可以用一致的語言和工作流程去管理他們。

<br>

{{< image src="/images/terraform/terraform.jpg"  width="800" caption="Terraform [什麼是 Terraform 的基礎設施即代碼？](https://learn.hashicorp.com/tutorials/terraform/infrastructure-as-code?in=terraform/aws-get-started)" src_s="/images/terraform/terraform.jpg" src_l="/images/terraform/terraform.jpg" >}}

<br>

## 安裝 Terraform

安裝 Terraform 的方式有很多種，我就以我在使用的 Mac OS 為例，其他可以參考 [Install Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/aws-get-started#install-terraform)：

<br>

### 安裝步驟

1. 先安裝 HashiCorp tap，這是 HashiCorp 在 Homebrew 的儲存庫：

```
brew tap hashicorp/tap
```

<br>

2. 使用 hashicorp/tap/terraform

```
brew install hashicorp/tap/terraform
```

<br>

### 如何驗證是否安裝成功

打開一個新的 Terminal，使用 `terraform -help` 檢查是否有安裝成功，也可以在 `-help` 後面加入參數來查看該參數的功能與更多訊息

<br>

{{< image src="/images/terraform/1.png"  width="800" caption="驗證 Terraform 安裝成功 [Install Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/aws-get-started#install-terraform)" src_s="/images/terraform/1.png" src_l="/images/terraform/1.png" >}}

<br>

### 快速入門

當我們安裝好，想要最快的了解 Terraform ，當然是自己動手做一次，我們依照官網的教學，可以在一分鐘內使用 Docker 配置好 NGINX 伺服器，那我們開始囉！

<br>

1. 首先，我們必須要先安裝好 Docker，下載 [Mac 版 Docker 桌面](https://docs.docker.com/desktop/install/mac-install/)
2. 建立一個資料夾，並進入該資料夾內
3. 將以下 Terraform 配置文件貼到檔案中，並取名 `main.tf` ([同步到 GitHub 需要程式碼的可以前往查看](https://github.com/880831ian/terraform/tree/master/docker_nginx))

```
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 2.13.0"
    }
  }
}

provider "docker" {}

resource "docker_image" "nginx" {
  name         = "nginx:1.23"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.name
  name  = "nginx"
  ports {
    internal = 80
    external = 8000
  }
}
```
(以上程式碼來自官網 [安裝 Terraform#快速入門](https://learn.hashicorp.com/tutorials/terraform/install-cli?in=terraform/aws-get-started#quick-start-tutorial) 加上小修改)

<br>

先來簡單說明一下 Terraform 程式碼格式，Terraform 的檔案副檔名是 `*.tf`，而基本的一個 Terraform 配置是透過叫做 HCL 語言撰寫。

`provider` 是決定要對哪一個平台或是提供商進行操作，`resource` 的欄位我們用下方程式來做說明：

```
resource 雲端資源名稱 自定義的名稱 {
 	 屬性 = 值
}
```

這邊的雲端資源名稱是固定的，所以不能隨意更改，要依照你要使用的提供商所給的資源來做設定！

<br>

所以我們已上面的 Docker 配置好 NGINX 伺服器為例，provider 我們這次使用的是 `docker`， resource 我們可以拆開來寫，

像是第一個 resource `docker_image` 我們幫他取叫 nginx，裡面就是放有關 image 的設定，詳細的 image 設定可以參考 [Resource (docker_image)](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs/resources/image)，

第二個 resource `docker_container` 一樣叫 nginx，裡面用的 image 是拿前面的 docker_image resource name 來使用，一樣詳細可以參考 [Resource (docker_container)](https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs/resources/container)。

<br>

接著有幾個指令要帶大家認識：


* `terraform init`：初始化項目，下載 tf 檔案中所需要的外掛套件
* `terraform plan`：會產生一份執行計劃。上面會寫著它將會做哪些事，你可以驗證是否符合你預期的設計
* `terraform apply`：實際運作，把基礎架構建置完成。在完成之後，會把目前的狀態儲存到一份檔案中
* `terraform destroy`：會銷毀用 Terraform 起的服務

附上懶人指令
```
alias ti='terraform init'
alias ta='terraform apply'
alias tp='terraform plan'
alias td='terraform destroy'
```

<br>

有上面的指令後，我們來實際操作看看：

<br>

1. 首先到該 `main.tf` 檔案目錄下，使用 `terraform apply` 來運作看看：

<br>

{{< image src="/images/terraform/2.png"  width="900" caption="無法直接執行 apply " src_s="/images/terraform/2.png" src_l="/images/terraform/2.png" >}}

<br>

會發現沒有辦法直接用 `terraform apply` 指令來建置服務，我們看一下他提示的說明，他說需要先進行初始化才可以執行！所以我們的建置流程是先 init --> apply

<br>

2. 我們先執行 `terraform init`，可以看到他會下載 tf 檔案中所需要的外掛套件 (docker)

<br>

{{< image src="/images/terraform/3.png"  width="800" caption="terraform init" src_s="/images/terraform/3.png" src_l="/images/terraform/3.png" >}}

<br>

3. 接著我們使用 `terraform plan` 來查看我們的計劃，可以看到他會列出我們所寫的 tf 裡面有用到的 resource，除了我們有設定的屬性，其他的屬性也會顯示出來，可以更方便地讓我們知道這個 resource 有哪些屬性可以設定

<br>

{{< image src="/images/terraform/4.png"  width="700" caption="terraform plan" src_s="/images/terraform/4.png" src_l="/images/terraform/4.png" >}}

<br>

4. 最後我們檢查都沒有問題，就可以使用 `terraform apply` 來建置，apply 其實跟 plan 一樣都會先讓我們看一下計劃，但會跳出詢問是否要執行，除非你輸入 `yes`，否則就跟 plan 單純顯示計劃內容，最後我們就可以看到他成功在 docker 上面建立 nginx 服務

<br>

{{< image src="/images/terraform/5.png"  width="800" caption="terraform apply" src_s="/images/terraform/5.png" src_l="/images/terraform/5.png" >}}
{{< image src="/images/terraform/6.png"  width="800" caption="查看 docker nginx 以及檢查其服務" src_s="/images/terraform/6.png" src_l="/images/terraform/6.png" >}}

<br>

5. 另外，當你想要移除服務時，可以使用 `terraform destroy` 來將服務給移除

<br>

{{< image src="/images/terraform/7.png"  width="700" caption="terraform destroy" src_s="/images/terraform/7.png" src_l="/images/terraform/7.png" >}}

<br>

## 後續文章

經過上面的步驟，了解 IaC Infrastructure as Code (基礎設施即代碼) 以及實際去使用 Terraform 去建置 docker 的 nginx 服務，之後還會更新有關 Terraform 的文章，會不定時更新此篇文章的後續文章連結區，大家有興趣可以多留意歐！

<br>

## 參考資料

[初探 Infrastructure as Code 工具 Terraform vs Pulumi](https://blog.wu-boy.com/2021/02/introduction-to-infrastructure-as-code-terraform-vs-pulumi/)

[今晚我想認識 Terraform](https://ithelp.ithome.com.tw/articles/10233759)