---
weight: 4
title: "部署 Laravel 於 Heroku 搭配 GitLab CI/CD"
date: 2022-05-26T15:07:00+08:00
lastmod: 2022-05-26T15:07:00+08:00
draft: false
author: "PinYi"
authorLink: "https://pin-yi.me"
description: "此文章會實作如何部署 Laravel 於 Heroku 搭配 GitLab CI/CD"
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["GitLab", "CI/CD", "Laravel",  "Heroku", "介紹", "實作"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

經過上一篇文章 [如何從頭打造專屬的 GitLab CI/CD](https://pin-yi.me/gitlab-cicd/) 的學習，讓我們了解到 GitLab CI/CD 的整個流程，接著我們本次要把 Laravel 給部署到 Heroku 透過 GitLab 的 CI/CD 去達成，不需要透過任何人工去測試，並上架程式到 HeroKu 上，全部都依賴 GitLab CI/CD，讓我們接著看下去吧！

<br>

當然，此文章程式碼也會同步到 Github ，需要的也可以去查看歐！要記得先確定一下自己的版本 [Github 程式碼連結](https://github.com/880831ian/Laravel-GitLab-CICD-Heroku) 😏

## 版本資訊

* macOS：11.6
* Docker：Docker version 20.10.14, build a224086
* Laravel Installer：2.3.0 
* Laravel Framework：9.14.1

<br>

首先，我們第一步驟就是先建立一個 Laravel 專案，至於為什麼要選擇用 Laravel 來當作 GitLab CI/CD 的範例呢？因為 Laravel 內建有 PHPUnit 的測試腳本，可以讓我們在 CI 測試時，更好的展現 CI 的功能！，有關於 Laravel 相關內容，這邊一樣推薦兩篇文章給大家閱讀：🤣

* [Laravel 介紹 (使用 Laravel 從零到有開發出一個留言板功能並搭配 RESTful API 來實現 CRUD)](https://pin-yi.me/laravel/)
* [Laravel 進階 (內建會員系統、驗證 RESTful API 是否登入、使用 Repository 設計模式)](https://pin-yi.me/laravel-advanced/)

又工商了一波 XD

<br>

## 建立 Laravel 專案

請大家依照 Laravel 官方文件來建立 Laravel 環境，也可以看小弟我的文章拉 👆👆👆，請記得要先安裝好 php 以及 composer，接著按照以下步驟來建立。

<br>

{{< image src="/images/laravel-gitlab-cicd-heroku/create.png"  width="900" caption="新建一個 Laravel 新專案" src_s="/images/laravel-gitlab-cicd-heroku/create.png" src_l="/images/laravel-gitlab-cicd-heroku/create.png" >}}

<br>

這時候瀏覽 `http://127.0.0.1:8000`，如果都正確，應該會看到 Laravel 的首頁

<br>

{{< image src="/images/laravel-gitlab-cicd-heroku/laravel.png"  width="900" caption="Laravel 首頁" src_s="/images/laravel-gitlab-cicd-heroku/laravel.png" src_l="/images/laravel-gitlab-cicd-heroku/laravel.png" >}}

<br>

## 測試本地 Unit Test

接著我們剛剛有提到選用 Laravel 的原因是 Laravel 有 PHPUnit 單元測試可以使用，所以我們現在先在本地端來測試 Unit Test，專案預設有放一個單元測試在 `tests/Unit/ExampleTest.php`。我們先再次確認環境是否有安裝好，再來執行單元測試。

<br>

{{< image src="/images/laravel-gitlab-cicd-heroku/unittest.png"  width="900" caption="在本地端執行單元測試" src_s="/images/laravel-gitlab-cicd-heroku/unittest.png" src_l="/images/laravel-gitlab-cicd-heroku/unittest.png" >}}

<br>

執行後，應該都會是通過的畫面，如下圖：

<br>

{{< image src="/images/laravel-gitlab-cicd-heroku/demo_test.png"  width="900" caption="執行單元測試結果" src_s="/images/laravel-gitlab-cicd-heroku/demo_test.png" src_l="/images/laravel-gitlab-cicd-heroku/demo_test.png" >}}

<br>

## GitLab CI/CD 建置

### 上傳 Laravel 專案

接下來我們要上傳含有 Unit Test 專案到 GitLab 上，步驟如下，如果已經熟悉如何將專案推到 GitLab，可以直接跳到 [在 GitLab 上執行單元測試](#在-gitlab-上執行單元測試)

1. 在 [gitlab.com](https://gitlab.com) 上點選建立專案，選擇 **Create blank project**，也可以直接瀏覽該網址 `https://gitlab.com/projects/new#blank_project`。
2.   輸入專案名稱可以選擇 Project deployment target 為 **Heroko**，選擇 Public，最後按下 **Create project**

<br>

{{< image src="/images/laravel-gitlab-cicd-heroku/gitlab.png"  width="800" caption="在 GitLab 上建立新專案" src_s="/images/laravel-gitlab-cicd-heroku/gitlab.png" src_l="/images/laravel-gitlab-cicd-heroku/gitlab.png" >}}

<br>

3. 於專案資料夾下加入 remote 遠端 GitLab，並 Push 將專案推上去。

<br>

{{< image src="/images/laravel-gitlab-cicd-heroku/gitlab_push.png"  width="800" caption="將 Laravel 專案推到 GitLab 上" src_s="/images/laravel-gitlab-cicd-heroku/gitlab_push.png" src_l="/images/laravel-gitlab-cicd-heroku/gitlab_push.png" >}}

<br>

成功推上去，可以到 GitLab 上，看到我們剛剛的專案！

<br>

{{< image src="/images/laravel-gitlab-cicd-heroku/gitlab_show.png"  width="800" caption="成功推到 GitLab 上" src_s="/images/laravel-gitlab-cicd-heroku/gitlab_show.png" src_l="/images/laravel-gitlab-cicd-heroku/gitlab_show.png" >}}

<br>

### 在 GitLab 上執行單元測試

要在 GitLab 上執行 CI/CD 就需要有 Runner，這次我們選擇使用 [gitlab.com](https://gitlab.com) 的 Shared runners，需要使用 Specific runners，可以查看上一篇 [如何從頭打造專屬的 GitLab CI/CD](https://pin-yi.me/gitlab-cicd/#%E8%87%AA%E6%9E%B6-runner-specific-runners) 文章

<br>

{{< image src="/images/laravel-gitlab-cicd-heroku/runner.png"  width="800" caption="本次使用 Share runners" src_s="/images/laravel-gitlab-cicd-heroku/runner.png" src_l="/images/laravel-gitlab-cicd-heroku/runner.png" >}}

<br>

接下來在專案的根目錄撰寫我們的 `.gitlab-ci.yml` 檔案，之後再次上傳 GitLab，當我們根目錄有此檔案，GitLab-CI 就會讀取並依照內容啟動 Runner 來執行工作：

* .gitlab-ci.yml
```yml
image: lorisleiva/laravel-docker:latest

Unit_test:
  stage: Unit_test
  script:
    - composer install --prefer-dist --no-ansi --no-interaction --no-progress --no-scripts
    - ./vendor/bin/phpunit --testsuit Unit --coverage-text --colors=never
```
說明一下這個 `yml` 檔內的設定是在做什麼：

待續....明天繼續努力 >< 


<br>

## 參考資料

[Gitlab-CI 入門實作教學 - 單元測試篇](https://nick-chen.medium.com/gitlab-ci-%E5%85%A5%E9%96%80%E7%AD%86%E8%A8%98-%E5%96%AE%E5%85%83%E6%B8%AC%E8%A9%A6%E7%AF%87-156455e2ad9f)

[Gitlab-CI 自動化部屬部署](https://medium.com/@nick03008/%E6%95%99%E5%AD%B8-gitlab-ci-%E5%85%A5%E9%96%80%E5%AF%A6%E4%BD%9C-%E8%87%AA%E5%8B%95%E5%8C%96%E9%83%A8%E7%BD%B2%E7%AF%87-ci-cd-%E7%B3%BB%E5%88%97%E5%88%86%E4%BA%AB%E6%96%87-cbb5100a73d4)

[部署 Laravel 於 Heroku 搭配 Gitlab CI/CD](https://medium.com/@vip131430g/%E9%83%A8%E7%BD%B2-laravel-%E6%96%BC-heroku-%E6%90%AD%E9%85%8D-gitlab-ci-cd-6d59a66aebdb)
