---
weight: 4
title: "如何過濾 GCP LOG，減少 Cloud Logging API 的花費"
date: 2023-07-30T20:10:00+08:00
lastmod: 2023-07-30T20:10:00+08:00
draft: false
author: "PinYi"
authorLink: "https://pin-yi.me"
description: ""
resources:
  - name: "featured-image"
    src: "featured-image.webp"
  - name: "featured-image-preview"
    src: "featured-image-preview.webp"

tags: ["GCP", "Cloud Logging"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

當我們使用 GCP 的 Cloud Logging 服務來查看 Log 時，有時候會有一些我們不需要顯示出來的，或是從來都不會去查詢的 Log，再者是 GCP 本身的錯誤導致大量噴錯的 Log ，這些 Log 都會導致 Cloud Logging 的費用增加。

## 介紹 Cloud Logging

先來簡單說一下 Cloud Logging 這項服務的基本架構，看圖：

<br>

{{< image src="/images/gcp-log-reduce-cloud-logging-api/overview.png"  width="700" caption="Cloud Logging 基本架構" src_s="/images/gcp-log-reduce-cloud-logging-api/overview.png" src_l="/images/gcp-log-reduce-cloud-logging-api/overview.png" >}}

<br>

可以看到 Logs Data 會透過 API 再經過 \_Default log sink (router) 存到相應命名的 log bucket (預設配置)，圖中 \_Required 以及 \_Default 的 log sink 都是 GCP 自動創建的接收器，下面簡述一下它們的區別：

<br>

- \_Required 日誌儲存桶

  Cloud Logging 會將以下類型的 Log 存到 \_Required 儲存桶

  1.  管理員活動審核 Log

  2.  系統事件審核 Log

  3.  Access Transparency Log

  Cloud Logging 會將 \_Required 儲存桶 Log 保留 400 天，無法調整該期限，且無法修改或刪除 \_Required 儲存桶，也沒辦法停用 \_Required log sink 接受器路由到 \_Required 儲存桶的設定。

<br>

- \_Default 日誌儲存桶

  只要不是存在 \_Required 日誌儲存桶的 Log 就會透過 \_Default log sink 接受器路由到 \_Default 儲存桶。

  除非有另外配置自定義設定， 否則 \_Default 日誌儲存桶 Log 只會保留 30 天，也一樣無法刪除 \_Default 日誌儲存桶。此外 Cloud Logging 的費用是以存在 \_Default 日誌儲存桶來計算。

<br>

| 功能         | 價格                                   | 每月免費額度                            |
| ------------ | -------------------------------------- | --------------------------------------- |
| Logging 提取 | 提取的 Log $0.5/GiB                    | 每個項目前 50 GiB                       |
| Logging 儲存 | 保留超過 30 天的 Log，每月每 GiB $0.01 | 在默認保留期限的 Log 不會有額外儲存費用 |

查看該專案使用的 Cloud Logging API 費用：https://console.cloud.google.com/apis/api/logging.googleapis.com/cost

<br>

## 過濾 Log

那現在知道 Cloud Logging 的架構，那當我們遇到需要過濾 Log 時，我們可以使用以下步驟來過濾以節省 Cloud Logging API 費用：

說明：這次範例是 google 在 2023/07/06 所發佈的 Service Health，當 GKE 版本大於 1.24 以上，就會噴

1. Failed to get record: decoder: failed to decode payload: EOF

2. cannot parse '0609 05:31:59.491654' after %L

3. invalid time format %m%d %H:%M:%S.%L%z for '0609 05:31:59.490647'

這三種類型的錯誤 Log，目前官方是建議先將他過濾掉，避免一直刷噴錢 >w<

Service Health：[https://status.cloud.google.com/incidents/y5XvpyBXFhsphSt4DfiE](https://status.cloud.google.com/incidents/y5XvpyBXFhsphSt4DfiE)

<br>

我們在上面架構圖有說到，Log 會透過 log sink 路由到 bucket，所以我們要將過濾條件加在 log router 上：

進入 Cloud Logging > 選擇 Log Router

<br>

{{< image src="/images/gcp-log-reduce-cloud-logging-api/1.png"  width="600" caption="選擇 Log Router" src_s="/images/gcp-log-reduce-cloud-logging-api/1.png" src_l="/images/gcp-log-reduce-cloud-logging-api/1.png" >}}

<br>

選擇 \_Default 的 Log Router Sinks，點選右邊編輯按鈕

<br>

{{< image src="/images/gcp-log-reduce-cloud-logging-api/2.png"  width="900" caption="選擇 _Default 的 Log Router Sinks" src_s="/images/gcp-log-reduce-cloud-logging-api/2.png" src_l="/images/gcp-log-reduce-cloud-logging-api/2.png" >}}

<br>

1. 第一個是 details，可以輸入說明，這邊輸入：google 有 bug 會噴大量的意外 LOG，怕費用飆高，先用官方建議的來過濾 LOG，詳細可以看： https://status.cloud.google.com/incidents/y5XvpyBXFhsphSt4DfiE

<br>

{{< image src="/images/gcp-log-reduce-cloud-logging-api/3.png"  width="550" caption="輸入 Sink details 說明" src_s="/images/gcp-log-reduce-cloud-logging-api/3.png" src_l="/images/gcp-log-reduce-cloud-logging-api/3.png" >}}

<br>

2. 接著 sink 服務選擇 Logging bucket，以及對應儲存的 log bucket (這邊基本上都是預設)

<br>

{{< image src="/images/gcp-log-reduce-cloud-logging-api/4.png"  width="550" caption="選擇 Logging bucket" src_s="/images/gcp-log-reduce-cloud-logging-api/4.png" src_l="/images/gcp-log-reduce-cloud-logging-api/4.png" >}}

<br>

3. 選擇那些可以被 include 到 sink 接收器的 LOG 格式 (這邊基本上都是預設)

<br>

{{< image src="/images/gcp-log-reduce-cloud-logging-api/5.png"  width="550" caption="預設的 LOG 格式" src_s="/images/gcp-log-reduce-cloud-logging-api/5.png" src_l="/images/gcp-log-reduce-cloud-logging-api/5.png" >}}

<br>

4.  這邊就是我們要輸入過濾的地方，先點擊 ADD EXCLUSION，輸入過濾的名稱，以及過濾的內容格式，我們輸入 google 在 Service Health 所提供的格式，最後按下 UPDATE SINK

<br>

{{< image src="/images/gcp-log-reduce-cloud-logging-api/6.png"  width="550" caption="新增要過濾的 LOG 格式" src_s="/images/gcp-log-reduce-cloud-logging-api/6.png" src_l="/images/gcp-log-reduce-cloud-logging-api/6.png" >}}

<br>

5. 等待更新完成，就可以看到我們已經在接收器上設定好過濾條件囉～

<br>

{{< image src="/images/gcp-log-reduce-cloud-logging-api/7.png"  width="900" caption="查看詳細接收器設定" src_s="/images/gcp-log-reduce-cloud-logging-api/7.png" src_l="/images/gcp-log-reduce-cloud-logging-api/7.png" >}}

<br>

6. 最後再檢查一下 Log 是不是沒有收到該錯誤的 Log 內容

<br>

{{< image src="/images/gcp-log-reduce-cloud-logging-api/8.png"  width="900" caption="檢查 LOG 是否不會再出現" src_s="/images/gcp-log-reduce-cloud-logging-api/8.png" src_l="/images/gcp-log-reduce-cloud-logging-api/8.png" >}}
