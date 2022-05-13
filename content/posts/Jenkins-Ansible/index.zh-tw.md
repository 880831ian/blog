---
weight: 4
title: "Jenkins 及 Ansible IT 自動化 CI/CD 介紹"
date: 2022-05-11T15:11:30+08:00
lastmod: 2022-05-11T15:11:30+08:00
draft: false
author: "PinYi"
authorLink: "https://pin-yi.me"
description: "此文章是介紹參考網路上 30 天入門 Ansible 及 Jenkins，自己實際測試後的筆記紀錄，歡迎大家可以先閱讀原文，原文會放在文章最後，歡迎大家指教 😁"
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["Ansible", "Jenkins", "CI/CD", "介紹", "實作"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

在軟體開發領域中，IT 自動化 (automation) 及 持續整合 (Continuous Integration, CI) 、持續佈署 (Continuous Deployment,CD) 是 DevOps 精神中很重要的兩個部分，此文章是參考 [30 天入門 Ansible 及 Jenkins](https://ithelp.ithome.com.tw/users/20103346/ironman/1473) ，再加上自己測試後的筆記紀錄，歡迎大家可先閱讀作者原文，那我們就開始一起學習吧 👊

我會分別介紹 [Ansible](https://www.ansible.com/) 與 [Jenkins](https://www.jenkins.io/) 這兩個非常熱門的開源軟體再搭配實作來讓大家更了解他們，那在之前我們先來聊聊為什麼需要 IT 自動化以及什麼是持續整合/持續部署吧 🤠

<br>

## 為什麼需要 IT 自動化 ?

當我們在開發任何軟體產品時，除了開發本身的過程需要花相當多的心力外，在產品部署的環節也是讓大家頭痛的一個部分。其環境的搭建或是參數的設定常常會因為一些小原因導致產品無法像在開發時一樣正常運作。尤其當需要部署的主機不只一台時，重複性的工作會花費我們大量的時間。再加上還會因為伺服器提供的作業環境不同、或是其他種種限制而必須做參數上的調整等等。

這時候 ` IT 自動化` 就顯得十分重要，透過自動化，不但可以幫助開發人員有效減少部署產品所需時間外，還可以在有限度的修改下分別針對不同環境去做調整。

<br>

{{< image src="/images/Jenkins-Ansible/automation.webp"  width="700" caption="[Best Automation Tools for DevOps](https://www.xenonstack.com/blog/best-automation-tools-for-devops)" src_s="/images/Jenkins-Ansible/automation.webp" src_l="/images/Jenkins-Ansible/automation.webp" >}}

<br>

## 什麼是持續整合/持續部署

當環境搭建成功後，對於服務本身的維護以及監控也是開發流程中相當重要的一環。當我們從原始碼代管服務 (GitHub、GitLab)上取得原始碼後，要如何確保產品在發布前品質沒有問題，一直以來都是開發人員需要思考的一個課題。

由於現在多數開發團隊都會透過版本控制來提交並整合開發人員各自修改的程式碼，若在合併分支時沒有把合併衝突 (conflict) 處理恰當，或是合併程式碼後產生某些邏輯錯誤，往往會到產品發佈後才發現不可預期的錯誤。

所以有了持續整合/持續部署的機制下，我們可以透過高頻率的整合、測試並分析程式碼品質，在最短時間發現問題以及發生點，進而確保產品每一次的發布都是穩定且高品質的。

<br>

{{< image src="/images/Jenkins-Ansible/CICD.png"  width="700" caption="CI/CD 流程圖 (作者打錯是 rsync 不是 rsyne) [Day12 什麼是 CICD](https://ithelp.ithome.com.tw/articles/10219083)" src_s="/images/Jenkins-Ansible/CICD.png" src_l="/images/Jenkins-Ansible/CICD.png" >}}

<br> 

上面這張圖是簡化版的 CI/CD 流程圖，當我們 Developer 將程式 Push 到原始碼代管服務 (GitHub、GitLab)上，會經過 Webhook 給 Jenkins 這種自動化可以持續整合部署到各自的伺服器上。

那 CI & CD 分別負責哪些工作呢？

### 持續整合 Continuous Integration

持續整合的英文是 Continuous Integration 我們縮寫成 CI ，後續也會使用 CI 來做說明：

* 流程：

1. 程式建置

	開發人員在每一次的 Commit & Push 後，都能夠於統一的環境自動 Build 程式，透過此步驟可以避免每個開發人員因本機的環境或是套件版本不同導致出現異常。
2. 程式測試

	當程式編譯完後，透過**單元測試**測試新寫的功能是否正確，或者確定是否會影響現有功能，透過該步驟進行測試，可以避免開發人員遺忘先在本機檢查，作為**雙重驗證**之功用。	
* 目的：
	1. 降低人為疏失風險
	2. 減少人工手動的反覆動作
	3. 進行版本控制
	4. 增加系統一制性與透明化

<br>

### 持續佈署 Continuous Deployment

持續佈署的英文是 Continuous Deployment 我們縮寫成 CD ，後續也會使用 CD 來做說明：

* 流程：

1. 部署服務

	透過自動化方式，將寫好的程式碼更新到機器上並公開對外服務，另外需要確保套件版本＆資料庫資料的完整性，也會透過監控系統進行服務存活檢查，若服務異常會即時發送通知告知開發人員。

* 目的：
	1. 保持每次更新程式都可以順暢完成
	2. 確保服務存活


<br>

我們了解了自動化與 CI/CD 的重要性與功用，那我們要怎麼去實現這些呢！我們先看下面這張圖：

<br>

{{< image src="/images/Jenkins-Ansible/devops.webp"  width="800" caption="[Top 5 DevOps Automation Tools in 2020](https://plumlogix.com/top-5-devops-automation-tools-in-2020/)" src_s="/images/Jenkins-Ansible/devops.webp" src_l="/images/Jenkins-Ansible/devops.webp" >}}
這邊整理 2020 年適合用於 DevOps Automation 的工具，那我們本次教學會介紹最多人使用的 Jenkins 以及 Ansible 兩種：

<br> 

## Jenkins

Jenkins 是使用 Java 編成語言編寫最受歡迎的開源自動化服務器。它促進了軟體開發過程中的持續整合、持續部署的自動化過程。

Jenkins 支持 1800 多個其他軟體套件，Jenkins 易於安裝和使用，它還提供方便瀏覽的項目管理儀表板，它的優點還有：
* 免費開源
* 充滿活力的用戶社群
* 多種工具和技術集成
* 插件支持
* 易於安裝、配置和升級
* 監控外部工作
* 支持各種身份驗證方法、通知、版本控制等等

<br>

## Ansible

Ansible 是一種 IT 自動化工具。它可以部署軟體、配置系統，並編排更高級的自動化任務，例如 CD (持續部署) 或 RollingUpdate 零停機的滾動更新。

自動化簡化了複雜的任務，不僅使開發人員的工作更容易管理，也讓他們的注意力可以放在對團體更有價值的其他任務上。換句話說，它可以節省時間並提高效率。Ansible 使用簡單的 YAML 語法，且 Ansible 是一種輕量級且安全的解決方案，它的優點還有：
* 使用 Ansible 不需要任何特殊的編程技能，因為使用的是 YAML 語法
* Ansible 允許建立高複雜性的 IT 自動化。
* 因為不需要安裝其他套件，所以伺服器上有更多空間來容納應用服務的支援
* Ansible 在設計上非常簡單且可靠一制性。

<br>

## Jenkins 跟 Ansible 比較

| 名稱 | Jenkins | Ansible | 
| :---: | :---: | :---: |
| 安裝 | 易於安裝 | 較難安裝 | 
| 套件 | Jenkins 支持 1800 多種套件 | 支持較少套件 |
| 語言 | 支持 C、C++、Java、Perl、Python、Ruby 等 | 支持 C、Python、JavaScript、Ruby 等 |
| 費用 | Jenkins 是免費的 | Ansible 不是免費的，但有試用版 |
| 大小 | 重量級 | 輕量級 |
| 服務 | 基於伺服器的工具 | 基於雲上的工具 |


<br>

因為 Ansible 適用於雲上的工具，本次的實作就針對 Jenkins 來做實作與說明：

## Jenkins 安裝與實作


我這次會使用 Docker-compose 來進行安裝，除了 Docker 以外也有不同的安裝方式，可以參考 [Jenkins download and deployment](https://www.jenkins.io/download/)，本次使用的環境版本如下：

### 版本

* macOS：11.6
* Docker：Docker version 20.10.14, build a224086
* Jenkins：jenkins/jenkins:lts-jdk11
* yamllint：1.26.0

<br>

### 安裝

這邊會使用 Jenkins 提供的 [官方 LTS 映像檔](https://hub.docker.com/layers/jenkins/jenkins/jenkins/lts-jdk11/images/sha256-ec98cb8b367b0f9426f71345efe11e001c901704cea0e61fd91beb37af34ef98?context=explore) 來作為基底，因為我們要多安裝測試程式 [yamllint](https://yamllint.readthedocs.io/en/stable/index.html)，所以就自己寫一個 Docker-compose：(同樣的程式碼會放在 [GitHub](https://github.com/880831ian/Jenkins)，也直接包成映像檔放在 [DockerHub](https://hub.docker.com/r/880831ian/jenkins)，歡迎大家自行取用)

<br>

{{< admonition tip "yamlint">}}
[yamlint](https://github.com/adrienverge/yamllint)，它是語法檢查工具，可以用來檢查 yaml 檔案的語法是否正確以及符合規範，我們看一下實際操作的畫面：


<br>
 
{{< image src="/images/Jenkins-Ansible/yamllint.png"  width="800" caption="yamllint 測試" src_s="/images/Jenkins-Ansible/yamllint.png" src_l="/images/Jenkins-Ansible/yamllint.png" >}}
可以看到如果不符合 yaml 規範就會跳出錯誤訊息。
<br>

{{< /admonition >}}

<br>

接下來先看一下整個 Docker-compose 結構以及各參數：

```sh
.
├── Docker-compose.yaml
├── jenkins
│   └── Dockerfile
```

<br>

**Docker-compose.yaml**
```yaml
version: "3.8"

services:
  jenkins:
    build: ./jenkins/
    container_name: jenkins
    ports:
      - 8080:8080
      - 50000:50000
    restart: always
    volumes:
      - ./jenkins_home:/var/jenkins_home
```

參數說明：
* `build: ./jenkins/`：因為要先安裝 yamllint，所以使用 Dockerfile 另外寫。
* `container_name:jenkins`：容器的名稱。
* `ports: -8080:8080 - 50000:50000`：8080 是待會我們瀏覽儀表板會使用到的 Port，如果本機上 8080 已經被佔用，可以自行更換，50000 是 Jenkins 所使用的 Port。
* `restart: always`：當容器停止時，會自動重新啟動容器。
* `volumes: - ./jenkins_home:/var/jenkins_home`：掛載目錄，就算刪除容器一樣可以保留其他設定。我將啟動 `Docker-compose.yaml` 的資料夾下多一個 jenkins_home 與容器內 /var/jenkins_home 做映射，大家可以自己去調整。

<br>

**jenkins/Dockerfile**

```dockerfile
FROM jenkins/jenkins:lts-jdk11

LABEL maintainer="880831ian@gmail.com"

USER root

RUN apt-get upgrade -y\
    && apt-get update -y\
    && apt-get install yamllint -y
```
參數說明：
* `FROM`：我們使用 Jenkins 官方提供的 LTS 維護版本。
* `USER`：因為要先安裝東西，所以直接給 root 權限。
*   `RUN`：先升級完後，再更新，最後再裝 [yamllint](https://yamllint.readthedocs.io/en/stable/index.html)。(-y 是同意所以詢問)

<br>

最後使用 `docker-compose` 來執行：

```sh
$ docker-compose -d
```
要在 Docker-compose.yaml 資料夾下指令才有用。

<br>

下完指令後，他就會在背景開始安裝，可以試著用瀏覽器瀏覽 `http://localhost:8080`，查看有沒有跳出下面這個畫面：

<br>

{{< image src="/images/Jenkins-Ansible/unlock.png"  width="800" caption="瀏覽器訪問 `http://localhost:8080`" src_s="/images/Jenkins-Ansible/unlock.png" src_l="/images/Jenkins-Ansible/unlock.png" >}}

<br> 

我們看到它需要輸入一組 Administrator password，我們要使用 `docker logs` 來查看，會發現最後會有寫 Please use the following password to proceed to installation 的地方：

```sh
$ docker logs jenkins

*************************************************************
*************************************************************
*************************************************************

Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

70c0780f62a7441f90286be106908378

This may also be found at: /var/jenkins_home/secrets/initialAdminPassword

*************************************************************
*************************************************************
*************************************************************
```
其中的 `70c0780f62a7441f90286be106908378` ，就是我們的 Administrator password，直接複製並貼到欄位後按 Continue。

<br>

我們可以看到它詢問是否要安裝套件，我們選擇左邊 Install suggested plugins 安裝推薦的套件即可：

<br>

{{< image src="/images/Jenkins-Ansible/plugins.png"  width="800" caption="安裝推薦的套件" src_s="/images/Jenkins-Ansible/plugins.png" src_l="/images/Jenkins-Ansible/plugins.png" >}}

<br> 

等待它安裝套件，安裝完後會自動跳到註冊畫面：

<br>

{{< image src="/images/Jenkins-Ansible/install.png"  width="800" caption="等待安裝..." src_s="/images/Jenkins-Ansible/install.png" src_l="/images/Jenkins-Ansible/install.png" >}}

<br> 

輸入完基本的資料後，按 Save and Continue：

<br>

{{< image src="/images/Jenkins-Ansible/create_adminuser.png"  width="800" caption="創建 Admin 使用者" src_s="/images/Jenkins-Ansible/create_adminuser.png" src_l="/images/Jenkins-Ansible/create_adminuser.png" >}}

<br> 

最後看到下面這個畫面就代表我們安裝好囉！

<br>

{{< image src="/images/Jenkins-Ansible/done.png"  width="1200" caption="Jenkins 儀表板" src_s="/images/Jenkins-Ansible/done.png" src_l="/images/Jenkins-Ansible/done.png" >}}

<br> 

### 建立第一個 Jenkins Job

我們已經成功安裝好並進入到 Jenkins 儀表板，我們先來建立第一個 Job，它的功用是告訴我們系統檔案的即時使用狀況，讓我們對 Jenkins 有初步的了解：

1. 點選儀表板的新增作業或是 Create a Job：

<br>

{{< image src="/images/Jenkins-Ansible/create_job.png"  width="1200" caption="新增作業" src_s="/images/Jenkins-Ansible/create_job.png" src_l="/images/Jenkins-Ansible/create_job.png" >}}

<br> 

2. 輸入 Job 專案名稱並選擇建立 Free-Style 軟體專案：

<br>

{{< image src="/images/Jenkins-Ansible/disk.png"  width="1200" caption="設定 Job 專案" src_s="/images/Jenkins-Ansible/disk.png" src_l="/images/Jenkins-Ansible/disk.png" >}}
可以看到這邊有不同的專案類型可以選擇，**Free-Style** 以及 **Pipeline** 這兩種類型的專案基本上就涵蓋大部分的需求。**Free-Style** 類型的專案提供了非常大的彈性讓使用者來做原始碼管理以及建置。如果建置流程涉及多個專案，則可以使用 **Pipeline** 類型的專案來組合及定義建置邏輯。

<br> 

3. 接下來設定專案組態：

<br>

{{< image src="/images/Jenkins-Ansible/describe.png"  width="1200" caption="設定專案組態" src_s="/images/Jenkins-Ansible/describe.png" src_l="/images/Jenkins-Ansible/describe.png" >}}
要記得幫每一個專案都加上描述，讓其他人知道該專案的用途或是使用時機等。

<br>

接著，在**建置**的下拉式欄位選擇 `選擇建置步驟 > 執行 Shell`

<br>

{{< image src="/images/Jenkins-Ansible/shell.png"  width="1200" caption="設定專案組態" src_s="/images/Jenkins-Ansible/shell.png" src_l="/images/Jenkins-Ansible/shell.png" >}}

<br>

輸入 `df -h` 指令：

<br>
 
{{< image src="/images/Jenkins-Ansible/df-h.png"  width="1000" caption="設定專案組態" src_s="/images/Jenkins-Ansible/df-h.png" src_l="/images/Jenkins-Ansible/df-h.png" >}}

<br>

我們這邊透過建置 `執行 Shell` 這個建置步驟來告訴 Jenkins，未來這個專案被建置，就會執行 `df -h` 這個指令。

<br>

4. 專案組態設置完後，我們點選左邊的**馬上建置**來建置剛剛建好的專案，如果我們設定上沒有問題，應該會在左下角的**建置歷程**這邊看到我們的第一個建置紀錄：

<br>
 
{{< image src="/images/Jenkins-Ansible/build.png"  width="800" caption="建置專案" src_s="/images/Jenkins-Ansible/build.png" src_l="/images/Jenkins-Ansible/build.png" >}}

<br>

5. 點進去後，再點 **Console Output**，可以看到這次建置的結果：


<br>
 
{{< image src="/images/Jenkins-Ansible/console.png"  width="800" caption="建置專案" src_s="/images/Jenkins-Ansible/console.png" src_l="/images/Jenkins-Ansible/console.png" >}}
這樣我們的第一個 Jenkins Job 就設定完成囉！Jenkins 也確實的執行我們所設定的指令，並將系統的使用狀況呈現在終端機的輸出上。
<br>

以上就是我們第一個簡單的專案建置流程，當然，Jenkins 可以做到的事情不僅如此，在後面我們會透過安裝不同的套件來強化 Jenkins，來達到完整的持續整合 😁

<br>

## 原始碼管理與建置觸發程序

上面有提到 Jenkins 作為一個持續整合的工具，與原始碼管理系統的整合尤其重要。我們這一章節，會介紹如何在 Jenkins 上透過原始碼管理 (source code management,SCM) 系統，例如從 GitHub 獲得專案的原始碼，並設置建置觸發程序 (build triggers) 來實踐持續整合。

<br>

### 建置專案

#### HTTPS

接下來我們先建立一個新的 Job，選擇 Free-style 模式，這次要在原始碼管理裡面選擇 Git，在 Repositories > Repository URL 裡面輸入我們這次要測試的 [repository URL](https://github.com/880831ian/Jenkins)：

<br>
 
{{< image src="/images/Jenkins-Ansible/repository.png"  width="1000" caption="設定 Repository URL" src_s="/images/Jenkins-Ansible/repository.png" src_l="/images/Jenkins-Ansible/repository.png" >}}

<br>

#### SSH

如果我們想要透過 SSH 來存取專案，我們會遇到以下狀況：

<br>
 
{{< image src="/images/Jenkins-Ansible/ssh_error.png"  width="1000" caption="SSH 尚未設定錯誤訊息" src_s="/images/Jenkins-Ansible/ssh_error.png" src_l="/images/Jenkins-Ansible/ssh_error.png" >}}
會有錯誤訊息是因為我們還沒有把 Jenkins 與 GitHub 做 SSH 金鑰配對，所以 GitHub 拒絕 Jenkins 透過 SSH 存取。那要怎麼解決呢？

最簡單的方法就是在 Jenkins 主機下[建立 SSH 金鑰](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)，並[將公開金鑰 Key 加入到 GitHub 帳號中](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)，有需要的朋友可以再自行使用，該範例使用 HTTPS 來做設定。

<br>

### 建置觸發程序

由於我們還沒有定義任何建置的觸發程序，所以除非我們手動去操作 Jenkins，不然 Jenkins 並不會主動幫我們進行建置。因此，在**建置觸發程序**這個欄位內，我們可以自由設定我們希望 Jenkins 何時自動幫我們進行建置專案。那依照專案的屬性不同，我們也可以採用不同的建置時機。那最常見的有以下兩種：

<br>

#### 定期建置

在 Jenkins 中，我們是採用 [Cron Format](https://cloud.google.com/scheduler/docs/configuring/cron-job-schedules) 的方式來定義建置行程。Cron Format 總共五個欄位，欄位與欄位之間可用空白或 Tab 鍵做區隔：

1. 分 (minute)：0 - 59 分
2. 時 (hour)：0 - 23 時
3. 日 (day of month)：1 - 31 日
4. 月 (month)：1 - 12 月
5. 星期 (day of week)：星期 0 - 7 (其中 0 與 7 都代表星期天)

假設我們希望每個 30 分鐘就建置一次當前專案，我們可以在定義規則裡面填入 `H/15 * * * *`：

<br>
 
{{< image src="/images/Jenkins-Ansible/cron.png"  width="1000" caption="定期建置" src_s="/images/Jenkins-Ansible/cron.png" src_l="/images/Jenkins-Ansible/cron.png" >}}

<br>

#### GitHub hook trigger for GITScm polling

這種觸發方式在持續整合時非常實用。我們可以讓 Jenkins 自動監測當在原始碼專案有任何的 push event 發生時就進行建置。為了要使用這種方式建置，需要以下幾個步驟來設定：

##### 新增 GitHub personal access token

1. 進入 GitHub 首頁，點選右上角下拉選單，點選 **Settings**：
 
{{< image src="/images/Jenkins-Ansible/setting_1.png"  width="1000" caption="GitHub hook trigger for GITScm polling 設定" src_s="/images/Jenkins-Ansible/setting_1.png" src_l="/images/Jenkins-Ansible/setting_1.png" >}}

<br>

2. 先點選左邊的 **Developer settings** > 點選左邊的 **Personal access tokens** > 點選右上角的 **Generate new token**，輸入 token 的描述並勾選 `repo` scope 以及 `admin:repo_hook` scope 跟 `admin:org_hook` scope，點選 **Generate token**：

<br>

{{< image src="/images/Jenkins-Ansible/setting_2.png"  width="700" caption="GitHub hook trigger for GITScm polling 設定" src_s="/images/Jenkins-Ansible/setting_2.png" src_l="/images/Jenkins-Ansible/setting_2.png" >}}

<br>

3. 會產生一個 token，請先把 token 複製下來，離開這個畫面後，token 就會看不到了！(此 token 已刪除 ✌️)

<br>

{{< image src="/images/Jenkins-Ansible/setting_3.png"  width="1000" caption="GitHub hook trigger for GITScm polling 設定" src_s="/images/Jenkins-Ansible/setting_3.png" src_l="/images/Jenkins-Ansible/setting_3.png" >}}

<br>

##### 設定 Jenkins GitHub

1. 進入 Jenkins 儀表板頁面，點選左邊**管理 Jenkins** > System Configuration 的**設定系統**，往下滑找到 GitHub：

<br>

{{< image src="/images/Jenkins-Ansible/setting_4.png"  width="1000" caption="GitHub hook trigger for GITScm polling 設定" src_s="/images/Jenkins-Ansible/setting_4.png" src_l="/images/Jenkins-Ansible/setting_4.png" >}}

<br>

2. 找到後，再 GitHub Servers 下拉欄位選擇 Add GitHub Server，會看到下面畫面，API URL 輸入 `https://api.github.com`，Credentials 點 Add：

<br>

{{< image src="/images/Jenkins-Ansible/setting_5.png"  width="1000" caption="GitHub hook trigger for GITScm polling 設定" src_s="/images/Jenkins-Ansible/setting_5.png" src_l="/images/Jenkins-Ansible/setting_5.png" >}}

<br>

3. Kind 選擇 Secret Text，Secret 輸入剛剛存的 Personal Access Token，Description 簡單描述一下：

<br>

{{< image src="/images/Jenkins-Ansible/setting_6.png"  width="1000" caption="GitHub hook trigger for GITScm polling 設定" src_s="/images/Jenkins-Ansible/setting_6.png" src_l="/images/Jenkins-Ansible/setting_6.png" >}}

<br>

4. 設定完後，點選一下右邊的 **Test connection**，如果我顯示 `Credentials verified for user UserName, rate limit: xxx` 就代表成功囉！

<br>

{{< image src="/images/Jenkins-Ansible/setting_7.png"  width="1000" caption="GitHub hook trigger for GITScm polling 設定" src_s="/images/Jenkins-Ansible/setting_7.png" src_l="/images/Jenkins-Ansible/setting_7.png" >}}

<br>

##### 設定專案組態

1. 接著我們跳回來剛剛的專案組態，並勾選 **GitHub hook trigger for GITScm polling**：

<br>

{{< image src="/images/Jenkins-Ansible/setting_8.png"  width="1000" caption="設定專案組態" src_s="/images/Jenkins-Ansible/setting_8.png" src_l="/images/Jenkins-Ansible/setting_8.png" >}}

<br>

2. 寫一個 Shell Script 來建置專案：

```sh
for file in $(find . -type f -name "*yaml")
do
	yamllint $file
done	
```

<br>

{{< image src="/images/Jenkins-Ansible/setting_9.png"  width="1000" caption="設定專案組態" src_s="/images/Jenkins-Ansible/setting_9.png" src_l="/images/Jenkins-Ansible/setting_9.png" >}}
這邊利用一個簡單的 Shell Script 迴圈來對所有 YAML file 進行 yamllint 的檢查，最後點選儲存離開。

<br>

##### GitHub 上整合 Jenkins

1. 先到 GitHub 被建置專案的頁面下點 **Setting** 標籤 > 點選左邊的 **Webhook**  > 點選右上角的 **Add webhook**  > 輸入 **Jenkins Hook URL** 到 Payload URL：

	記得 Jenkins Hook URL 後面要加 `/github-webhook/`，小弟我卡在這裡很久😢 😢

<br>

{{< image src="/images/Jenkins-Ansible/setting_10.png"  width="800" caption="Webhooks 設定" src_s="/images/Jenkins-Ansible/setting_10.png" src_l="/images/Jenkins-Ansible/setting_10.png" >}}

<br>

{{< admonition tip "Jenkins Hook URL 設定">}}
由於我們是將 Jenkins 運行在本機端，所以 Jenkins Hook URL `http://localhost:8080` 是 Private IP。GitHub 沒有辦法抓 Private IP，為了練習，我們可以透過 [ngrok](https://ngrok.com/) 這套簡單小工具來暫時將 `http://localhost:8080` 變成 Public IP。

[ngrok](https://ngrok.com/) 的使用方式很簡單，只要先下載 `brew install ngrok/ngrok/ngrok` ，並使用 `ngrok http 8080` 指令，將 Private IP 變成 Public IP：

<br>

{{< image src="/images/Jenkins-Ansible/setting_11.png"  width="1000" caption="ngrok 將 Private IP 變成 Public IP" src_s="/images/Jenkins-Ansible/setting_11.png" src_l="/images/Jenkins-Ansible/setting_11.png" >}}

<br>

圖片中 `https://2063-111-235-135-57.jp.ngrok.io` 就是 Public 的 Jenkins Hook URL

{{< /admonition >}}

<br>

完成後，先點選 **Recent Deliveries** 檢查是否成功，底下的 Response 需要是 <font color=green>200</font>，才是對的歐！(這裡一定要先檢查，不然後面會找問題到死 XD)

<br>

{{< image src="/images/Jenkins-Ansible/setting_12.png"  width="1000" caption="Webhooks 設定" src_s="/images/Jenkins-Ansible/setting_12.png" src_l="/images/Jenkins-Ansible/setting_12.png" >}}

<br>

### 測試

我們都設定好後，要開始來測試，我們可以直接先點選 **馬上建置**，來測試是否可以透過 **GitHub personal access token**，抓取 GitHub 的檔案。

<br>

{{< image src="/images/Jenkins-Ansible/test_1.png"  width="800" caption="馬上建置" src_s="/images/Jenkins-Ansible/test_1.png" src_l="/images/Jenkins-Ansible/test_1.png" >}}

<br>

可以看到在建置流程那邊發現建置失敗，點進去可以看詳細內容，

<br>

{{< image src="/images/Jenkins-Ansible/test_2.png"  width="800" caption="建置失敗" src_s="/images/Jenkins-Ansible/test_2.png" src_l="/images/Jenkins-Ansible/test_2.png" >}}

<br>

點選左側的 **Console Output**，可以看到我們有成功獲取 GitHub 上得專案，並且執行我們的 Shell 來檢查 yaml 的檔案格式，發現是因為格式有錯誤，所以建置才會失敗 ❌ 

<br>

{{< image src="/images/Jenkins-Ansible/test_3.png"  width="1000" caption="Console Output" src_s="/images/Jenkins-Ansible/test_3.png" src_l="/images/Jenkins-Ansible/test_3.png" >}}

<br>

接下來我們先修改一下 yaml 的檔案，後重新 push 到 Github 上，並觀察 Jenkins 會不會自動建置 ！(修改位置大家可以直接看 [Commit 結果](https://github.com/880831ian/Jenkins/commit/729556412ef8796477a351040604aad8c8083c05))

<br>

{{< image src="/images/Jenkins-Ansible/test_4.png"  width="1000" caption="Console Output" src_s="/images/Jenkins-Ansible/test_4.png" src_l="/images/Jenkins-Ansible/test_4.png" >}}
當你 push 完後，發現它會自動建置，請因為我們修改成正確格式，所以他也建置成功囉！

<br>

也可以點選左側有一個新的 **GitHub Hook Log** ，可以看到我們成功透過 **GitHub hook trigger for GITScm polling** 偵測到有新的 event，透過 WebHook 讓 Jenkins 知道。

<br>

{{< image src="/images/Jenkins-Ansible/test_5.png"  width="1000" caption="GitHub Hook Log" src_s="/images/Jenkins-Ansible/test_5.png" src_l="/images/Jenkins-Ansible/test_5.png" >}}

<br>

### 建置後觸發通知

當我們自動建置成功當然沒什麼問題，但如果失敗有可能就會影響後續程式的上線時間，所以我們希望建置完成後，可以收到通知，通知除了可以用 email 以外，也可以使用套件去串接我們常用的平台，例如 Telegram、Slack、Line 等等，接下來我會教大家要怎麼去串接這些服務，在開始之前要請大家先安裝兩個套件：

#### 安裝/設定 Build Timestamp

[Build Timestamp](https://plugins.jenkins.io/build-timestamp/) 這個套件可以幫我們在稍後傳送通知時加上當下的時間戳，那要怎麼安裝套件呢？先到儀表板首頁，點選左側的 **管理 Jenkins** > 點選 **管理外掛程式**：

<br>

{{< image src="/images/Jenkins-Ansible/trigger_1.png"  width="1200" caption="管理 Jenkins > 管理外掛程式" src_s="/images/Jenkins-Ansible/trigger_1.png" src_l="/images/Jenkins-Ansible/trigger_1.png" >}}

<br>

再 Plugin Manager 的 可用的裡面搜尋 Build Timestamp，選擇後點下方的 **Download now and install after restart**，等待他安裝後會自動重啟。

<br>

{{< image src="/images/Jenkins-Ansible/trigger_2.png"  width="1200" caption="安裝 Build Timestamp" src_s="/images/Jenkins-Ansible/trigger_2.png" src_l="/images/Jenkins-Ansible/trigger_2.png" >}}

<br>

重啟後從儀表板點選左側 **管理 Jenkins** > 點選 **設定系統**，找到 **Build Timestamp**，開啟設定，並設定 Timezone 為 `Asia/Taipei` 以及 pattern `yyyy-MM-dd HH:mm:ss z`，這樣我們待會就可以使用 `BUILD_TIMESTAMP` 參數來獲取當下時間，記得要按下儲存歐！

<br>

{{< image src="/images/Jenkins-Ansible/trigger_3.png"  width="1200" caption="設定 Build Timestamp" src_s="/images/Jenkins-Ansible/trigger_3.png" src_l="/images/Jenkins-Ansible/trigger_3.png" >}}

<br>

#### 安裝/設定 Notify.Events

[Notify.Events](https://plugins.jenkins.io/notify-events/) 這個套件可以串接很多的平台，例如 Telegram、Slack、Line 等，也可以透過它寄發郵件，是一個十分方便的套件，但缺點是他需要註冊，免費版只有每個月 300 次的訊息傳輸量，但在我們測試階段已經十分夠用。一樣我們用剛剛的方法安裝 **Notify.Events**。

<br>

{{< image src="/images/Jenkins-Ansible/trigger_4.png"  width="1200" caption="安裝 Notify.Events" src_s="/images/Jenkins-Ansible/trigger_4.png" src_l="/images/Jenkins-Ansible/trigger_4.png" >}}

<br>

安裝好後，Notify.Events 他不需要先設定，它可以依據不同的 Job 有不同的設定，所以我們開啟剛剛的 Job 組態，拉到最下面找到 **建置後動作** ，選擇 **Notify.Events**：

<br>

{{< image src="/images/Jenkins-Ansible/trigger_5.png"  width="1200" caption="安裝 Notify.Events" src_s="/images/Jenkins-Ansible/trigger_5.png" src_l="/images/Jenkins-Ansible/trigger_5.png" >}}
可以看到這邊要先輸入 Token，那 Token 就必須去官網註冊後設定。

<br>

我們先開啟瀏覽器，搜尋 [Notify.Events](https://notify.events/en)，註冊帳號後，在 Channels 點選 **Create**，輸入一下 Title 按下 **Save**。 

<br>

{{< image src="/images/Jenkins-Ansible/trigger_6.png"  width="1200" caption="Notify.Events 官網設定" src_s="/images/Jenkins-Ansible/trigger_6.png" src_l="/images/Jenkins-Ansible/trigger_6.png" >}}

<br>

完成後，應該可以看到以下畫面，這邊就可以讓我們選擇來源，以及要發送到哪裡：

<br>

{{< image src="/images/Jenkins-Ansible/trigger_7.png"  width="1200" caption="Notify.Events 官網設定" src_s="/images/Jenkins-Ansible/trigger_7.png" src_l="/images/Jenkins-Ansible/trigger_7.png" >}}

<br>

我們先選擇 Sources，點選 **Add source**，可以看到很多來源，選擇 **CI/CD and Version control** ，再選擇 **Jenkins**：

<br>

{{< image src="/images/Jenkins-Ansible/trigger_8.png"  width="1200" caption="Notify.Events 官網設定" src_s="/images/Jenkins-Ansible/trigger_8.png" src_l="/images/Jenkins-Ansible/trigger_8.png" >}}

<br>

點選 **Next**，就可以看到以下畫面，它告訴我們要將它提供的 `Token` 貼入設定檔，也就是我們剛剛在 Job 組態裡面的那個 Token：

<br>

{{< image src="/images/Jenkins-Ansible/trigger_9.png"  width="1200" caption="Notify.Events 官網設定" src_s="/images/Jenkins-Ansible/trigger_9.png" src_l="/images/Jenkins-Ansible/trigger_9.png" >}}

<br>

設定好 Sources，接下來要設定接收方，回到剛剛 Notify.Events 的儀表板，點選 **Subscribe**，我們測試使用 Telegram 來當接收方，它會跳出一個視窗，告訴你要怎麼把他的機器人加成好友或是加入群組，這邊就依大家需要自行選擇，那我就將它加入群組，使用 `/subscribe DRr0bIZ0 @NotifyEventsBot` 指令來綁定

<br>

{{< image src="/images/Jenkins-Ansible/trigger_10.png"  width="1200" caption="Notify.Events 官網設定" src_s="/images/Jenkins-Ansible/trigger_10.png" src_l="/images/Jenkins-Ansible/trigger_10.png" >}}

<br>

這時候我們都設定好了，我們回到 Job 組態的 Notify.Events 設定位置，將 Token 貼上去，它可以自訂訊息的模板，可以全部都一樣，也可以針對建置後的狀態，產生不同的訊息模板，我們來自定義設計一下：

**Success**

```
📢  Jenkins 建置通知 📣

時間：$BUILD_TIMESTAMP 🕐
名稱： <a href="$PROJECT_URL">$PROJECT_NAME</a>
次數： <a href="$BUILD_URL">#$BUILD_NUMBER</a>
建置狀態： 🟢 <b>$BUILD_STATUS</b> 🟢

<a href="$BUILD_URL/console">建置日誌連結</a>

--------- 😍😍😍  ---------
```

<br>

{{< image src="/images/Jenkins-Ansible/trigger_11.png"  width="1000" caption="Notify.Events Success" src_s="/images/Jenkins-Ansible/trigger_11.png" src_l="/images/Jenkins-Ansible/trigger_11.png" >}}

<br>

**Failure**

```
📢  Jenkins 建置通知 📣

時間：$BUILD_TIMESTAMP 🕐
名稱： <a href="$PROJECT_URL">$PROJECT_NAME</a>
次數： <a href="$BUILD_URL">#$BUILD_NUMBER</a>
建置狀態： 🔴  <b>$BUILD_STATUS</b> 🔴

<a href="$BUILD_URL/console">建置日誌連結</a>

--------- 😭😭😭  ---------
```

<br>

{{< image src="/images/Jenkins-Ansible/trigger_12.png"  width="1000" caption="Notify.Events Failure" src_s="/images/Jenkins-Ansible/trigger_12.png" src_l="/images/Jenkins-Ansible/trigger_12.png" >}}

<br>

#### Telegram 通知測試

最後我們都設定好了，就來測試一下吧！我們先故意將程式碼格式用錯，[讓他先跳出錯誤](https://github.com/880831ian/Jenkins/commit/e88d35d56f2b2d1b999682c0f748431999bb4b9e)，[再修改](https://github.com/880831ian/Jenkins/commit/ec0e9f9c2148cecde6f70ec391d4c775fc180029)，來看看結果如何吧！(文字連結是對應的 Commit )

<br>

{{< image src="/images/Jenkins-Ansible/trigger_13.png"  width="600" caption="Telegram 通知" src_s="/images/Jenkins-Ansible/trigger_13.png" src_l="/images/Jenkins-Ansible/trigger_13.png" >}}

<br>

可以看到，我們分別兩次的測試，會依據我們建置後的結果，觸發不同的通知模板。


<br>

## 參考資料

[30 天入門 Ansible 及 Jenkins](https://ithelp.ithome.com.tw/users/20103346/ironman/1473)

[Day12 什麼是 CICD](https://ithelp.ithome.com.tw/articles/10219083)

[Jenkins和Ansible的對比和區別](http://www.srcmini.com/21799.html)

[[CI]設定jenkins連結GitHub Private Repo by Webhook](https://medium.com/@BuzonXXXX/ci-%E8%A8%AD%E5%AE%9Ajenkins%E9%80%A3%E7%B5%90github-private-repo-111c53d29047)