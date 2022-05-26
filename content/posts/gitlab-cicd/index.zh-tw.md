---
weight: 4
title: "如何從頭打造專屬的 GitLab CI/CD 全攻略"
date: 2022-05-26T10:10:00+08:00
lastmod: 2022-05-26T10:10:00+08:00
draft: false
author: "PinYi"
authorLink: "https://pin-yi.me"
description: "此文章是介紹 GitLab CI/CD 的一些概念，並解搭配圖片來做説明💪"
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["GitLab", "CI/CD", "介紹", "實作"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

自從上次學完 Jenkins 及 Ansible CI/CD，就覺得 CI/CD 實在太酷了！能夠自動化的去持續整合 (Continuous Integration, CI) 以及持續佈署 (Continuous Deployment,CD) 專案，再加上這幾天複習了 Git 的使用方法，突然想到，要怎麼設定我們將程式推到遠端的 Git Repo，能夠再搭配 CI/CD 去做測試，並且把程式碼自動部署到正式的服務機器設備上呢？

<br>

那我們就開始囉！此篇會複習一下 CI/CD 並且說明 GitLab CI/CD 運作的原理，關於實作部分會放到下一篇文章 [部署 Laravel 於 Heroku 搭配 GitLab CI/CD](https://pin-yi.me/laravel-heroku-gitlab-cicd)

對了工商一下，剛剛有提到的 Jenkins 及 Ansible CI/CD 總共有 3 篇文章，還沒看過的可以先飛過去看一下歐 👇👇👇

[Jenkins 及 Ansible IT 自動化 CI/CD 介紹](https://pin-yi.me/jenkins-ansible/)

[使用 Jenkins 設定 GitHub 觸發程序並通知 Telegram Bot](https://pin-yi.me/jenkins/)

[Ansible 介紹與實作 (Inventory、Playbooks、Module、Template、Handlers)](https://pin-yi.me/ansible/)

<br>

老樣子文章也會同步到 Github，會附上範例中的程式碼，有需要的也可以去查看歐！ [Github 程式碼連結](https://github.com/880831ian/GitLab-CICD) 💓

<br>

## GitLab CI/CD

GitLab CI/CD 是 GitLab 內建強大的工具，在 GitHub 被稱為 Github Actions，這個之後有空再來介紹XD，回歸正題，GitLab CI/CD 可以讓我們持續整合和部署，且不需要使用第三方的應用程式來整合。我們來複習一下 `持續整合 CI`、`持續部署 CD ` 他們是什麼吧：

<br>

### 持續整合 CI

一個處於開發階段的專案或是軟體，它被我們放在 GitLab 的 Repository 裡，開發人員每天會推送不同的程式碼到 GitLab 上，GitLab 持續整合 CI 會在開發人員每次推送後，自動化的依照我們設定好的腳本進行建構與測試，從而減少開發中的專案發生錯誤的可能性。

這種做法就被稱作持續整合，對於我們提交給專案的每一個更改、甚至是開發分支，它都是自動且持續地建構和測試，確保新加入的變動，符合我們在專案中所設計的所有測試。

<br>

### 持續部署 CD

持續部署，讓我們不太需要手動的去部署專案服務，而是將其設置為自動化部署，完全不需要有人工去干涉，減少了人為的部署錯誤。

<br>

{{< image src="/images/gitlab-cicd/cicd.jpg"  width="800" caption="GitLab CI/CD (圖片：[GitLab Agile Planning](https://about.gitlab.com/solutions/agile-delivery/))" src_s="/images/gitlab-cicd/cicd.jpg" src_l="/images/gitlab-cicd/cicd.jpg" >}}

<br>

大致了解是如何運作之後，我們接著聊聊上面有提到的 `設定好的腳本`：

### .gitlab-ci.yml

GitLab CI/CD 的工作原理是，要在專案根目錄新增一個名為 `.gitlab-ci.yml` 的文件 (記得文件名稱開頭有 `.` ，我一開始忘記要加，想說怎麼都沒有反應 😅 )，也就是我們上面說的 `設定好的腳本`，可以先將所需建構、測試和部署的腳本編寫完成，以及定義很多規則，例如執行命令的先後順序、部署應用程式的位置以及指定是否自動運行或是手動觸發腳本等。

將 `.gitlab-ci.yml` 文件放入 Repository 裡，就會觸發 CI，負責管理的 GitLab-CI 就會依照 `.gitlab-ci.yml` 設定檔來啟動名為 `GitLab Runner` 的工具來運行腳本，這個 `GitLab Runner` 我們放到後面來說，我們先來說說 `.gitlab-ci.yml` 這個設定檔要怎麼編寫，以及編寫後的流程。

<br>

這是一個示範的 `.gitlab-ci.yml`，選自優良的 [GitLab](https://docs.gitlab.com/ee/ci/quick_start/) XD，為了說明有小修改程式碼，程式碼也會放在 [GitHub](https://github.com/880831ian/GitLab-CICD) 上歐：

* .gitlab-ci.yml

```yml
stages:
  - build
  - test
  - deploy

cache:
  paths:
    - config/

build-job:
  stage: build
  script:
    - echo "Hello, $GITLAB_USER_LOGIN!"

test-job1:
  stage: test
  script:
    - echo "This job tests something"

test-job2:
  stage: test
  before_script:
    - echo "This job tests something, but takes more time than test-job1."
  script:
    - echo "After the echo commands complete, it runs the sleep command for 20 seconds"
    - echo "which simulates a test that runs 20 seconds longer than test-job1"
    - sleep 20

deploy-prod:
  stage: deploy
  script:
    - echo "This job deploys something from the $CI_COMMIT_BRANCH branch."
```

<br>

那我來簡單說明一下上面這些設定檔案的功能：
* stages：代表這個 CI 設定檔有三個 stage 要跑，一個是 build、一個 test、一個 deploy，他們的順序也決定 CI 運作的順序，由 build > test > deploy，假如 test 沒有通過，就不會執行 deploy。
* cache：我們在寫 CI 時，常常需要裝 package，但我不想每次都重新跑一次，所以可以寫一個 cache，不要讓 GitLab 每次都重新拉新的 package。
* build-job、test-job1、test-job2、deploy-prod：代表我有 4 個 job 要執行，每個 job 裡面有不同的任務，也是顯示在 Pipeline 的名稱。
* stage：他現在要執行的階段，對應到 stages。
* before_script：可以把它當先需要先執行的指令，後面才會執行主要的 script 指令。所以需要安裝的可以先寫在這裡面。
* script：主指令，在實際運行的腳本中，通常會見到多行的指令被依序執行。
* $CI_COMMIT_BRANC：當然 `.gitlab-ci.yml` 檔案也可以帶入參數，這個部分我們留到 [部署 Laravel 於 Heroku 搭配 GitLab CI/CD](https://pin-yi.me/laravel-heroku-gitlab-cicd) 搭配實際操作來說明。

<br>

當我們將 `.gitlab-ci.yml` 連同專案一起推到 GitLab 上後，我們可以看到它會開始執行我們所寫的腳本，會顯示整個執行過程：

<br>

{{< image src="/images/gitlab-cicd/ci.png"  width="1000" caption="GitLab CI/CD 執行過程" src_s="/images/gitlab-cicd/ci.png" src_l="/images/gitlab-cicd/ci.png" >}}

<br>


查看執行的狀態：

<br>

{{< image src="/images/gitlab-cicd/status.png"  width="800" caption="GitLab CI/CD 狀態 " src_s="/images/gitlab-cicd/status.png" src_l="/images/gitlab-cicd/status.png" >}}

<br>

也可以在 GitLab Pipeline 看到執行的流程：

<br>

{{< image src="/images/gitlab-cicd/pipeline.png"  width="800" caption="GitLab CI/CD Pipeline " src_s="/images/gitlab-cicd/pipeline.png" src_l="/images/gitlab-cicd/pipeline.png" >}}

<br>

### GitLab CI Runner

我們上面有提到，我們在 CI 跑腳本，需要一個 Server 來代替 GitLab 來讓我們執行，這個 Server 我們稱為 Runner。我們來看一下整個執行的圖片：

<br>

{{< image src="/images/gitlab-cicd/gitlab-cicd.png"  width="600" caption="Gitlab CI/CD 實際執行流程 (圖片來源：[Gitlab-CI 入門實作教學 - 單元測試篇](https://nick-chen.medium.com/gitlab-ci-%E5%85%A5%E9%96%80%E7%AD%86%E8%A8%98-%E5%96%AE%E5%85%83%E6%B8%AC%E8%A9%A6%E7%AF%87-156455e2ad9f)) " src_s="/images/gitlab-cicd/gitlab-cicd.png" src_l="/images/gitlab-cicd/gitlab-cicd.png" >}}

<br>

那這個 Runner 有分成兩種：

* 共享 Runner (Shared Runners)
* 自架 Runner (Specific Runners)

因為我們本文章以及後續 [部署 Laravel 於 Heroku 搭配 GitLab CI/CD](https://pin-yi.me/laravel-heroku-gitlab-cicd) 文章所使用的平台是 [gitlab.com](https://gitlab.com)，由官方所提供，所以我們直接使用共享 Runner，可以在 repository Settings → CI / CD → Runners 中找到，有不少官方提供的共享 Runner 可以使用，也不需要做任何設定。

<br>

{{< image src="/images/gitlab-cicd/sharedrunners.png"  width="800" caption="GitLab CI/CD 共享 Runner" src_s="/images/gitlab-cicd/sharedrunners.png" src_l="/images/gitlab-cicd/sharedrunners.png" >}}

<br>

<br>

## 參考資料

[Get started with GitLab CI/CD](https://docs.gitlab.com/ee/ci/quick_start/)

[Best Practice for DevOps on GitLab and GCP : GitLab CI/CD - Day 6 -](https://ithelp.ithome.com.tw/articles/10214114)

[Gitlab-CI 入門實作教學 - 單元測試篇](https://nick-chen.medium.com/gitlab-ci-%E5%85%A5%E9%96%80%E7%AD%86%E8%A8%98-%E5%96%AE%E5%85%83%E6%B8%AC%E8%A9%A6%E7%AF%87-156455e2ad9f)

[如何使用 GitLab CI](https://medium.com/@mvpdw06/%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8-gitlab-ci-ebf0b68ce24b)