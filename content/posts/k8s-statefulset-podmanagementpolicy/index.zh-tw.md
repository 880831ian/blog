---
weight: 4
title: "在正式環境上踩到 StatefulSet 的雷，拿到 P1 的教訓"
date: 2023-10-20T18:13:00+08:00
lastmod: 2023-10-20T018:13:00+08:00
draft: false
author: "PinYi"
authorLink: "https://pin-yi.me"
description: ""
resources:
  - name: "featured-image"
    src: "featured-image.webp"
  - name: "featured-image-preview"
    src: "featured-image-preview.webp"

tags: ["Kubernetes", "K8s", "StatefulSet"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

此文章要來記錄一下最近在公司在正式環境踩到 StatefulSet 的雷，事情是這樣的，我們有些特定服務，是使用 StatefulSet 來建置，為什麼不用 Deployment，這個說來話長 (也不是因為需要特定的 Pod 名稱等等)，我們這邊先不討論，這個 StatefulSet 服務是 Nginx + PHP-FPM，所以我們在 StatefulSet 的 PHP Container 上有設定 readiness，readiness 的設定長得像以下：

<br>

```yaml
readinessProbe:
  exec:
    command:
      - "/bin/bash"
      - "-c"
      - |
        CHECK_INFO=$(curl -s -w 'http code:\t%{http_code}\n' 127.0.0.1/status)
        HTTP_CODE=$(echo -e "${CHECK_INFO}" | awk '/http code:/ {print $3}')
        IDLE_PROCESSES=$(echo -e "${CHECK_INFO}" | awk '/idle processes:/ {print $3}')
        [[ $HTTP_CODE -eq 200 && $IDLE_PROCESSES -ge 10 ]] || exit 1
```

<br>

我們會用 curl 來打 `/status`，並檢查回傳的 http code 是否為 200，以及 idle processes 是否大於等於 10，如果不符合，就會回傳 1，讓他被標記不健康，讓 Kubernetes 停止流量到不健康的容器，以確保流量被路由到其他健康的副本。

<br>

{{< image src="/images/k8s-statefulset-podmanagementpolicy/1.png"  width="900" caption="使用多層的 nginx proxy 處理" src_s="/images/k8s-statefulset-podmanagementpolicy/1.png" src_l="/images/k8s-statefulset-podmanagementpolicy/1.png" >}}
