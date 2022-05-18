---
weight: 4
title: "使用 Prometheus 和 Grafana 打造監控預警系統 (Docker 篇)"
date: 2022-05-18T12:18:00+08:00
lastmod: 2022-05-18T12:18:00+08:00
draft: false
author: "PinYi"
authorLink: "https://pin-yi.me"
description: "此文章是透過 Docker-compose 搭配 Nginx 實作一個簡單的 web service 範例，並整合 Prometheus 和 Grafana"
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["Prometheus", "Grafana", "Docker", "介紹", "實作"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

還記得我們上次架設 EFK 來獲得容器的日誌嗎！？身為一個 SRE 除了收集日誌外，還需要監控每個系統或是服務的運行狀況，並在警急情況即時通知相關人員作為應對處理。所以透過好的 Monitoring/Alert System 了解目前 Server 硬體系統狀況和整個 Service 的網路狀況是非常重要的一件事情。

在眾多的 Monitor 工具中，[Prometheus](https://prometheus.io/) 是一個很方便且完善的監控預警框架 TSDB (Time Series Database) 時間序列資料庫，可以快速且容易的建立不同維度的 metrics 和整合不同的 Alert Tool 以及資訊視覺化圖表的監控工具並提供自帶的 [PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/) 進行 query 查詢。

<br>

{{< image src="/images/Prometheus-Grafana-Docker/prometheus.png"  width="450" caption="Prometheus Logo" src_s="/images/Prometheus-Grafana-Docker/prometheus.png" src_l="/images/Prometheus-Grafana-Docker/prometheus.png" >}}

<br>

我們先來看看 Prometheus 的架構圖，可以更了解 Prometheus 整體的定位：

<br>

{{< image src="/images/Prometheus-Grafana-Docker/prometheus-architecture.jpg"  width="800" caption="Prometheus 架構圖 (圖片來源：[使用 Prometheus 和 Grafana 打造 Flask Web App 監控預警系統](https://blog.techbridge.cc/2019/08/26/how-to-use-prometheus-grafana-in-flask-app/))" src_s="/images/Prometheus-Grafana-Docker/prometheus-architecture.jpg" src_l="/images/Prometheus-Grafana-Docker/prometheus-architecture.jpg" >}}

<br>
 
1. 有一個 Prometheus server 主體，會去 Prometheus Client Pull 相關的 Metrics，若是短期的 Job 例如 CronJob 在還來不及 Pull 資料回來可能就已經完成任務了、清洗掉資料。所以會有一個 `pushgateway` 接收 Job Push 過來的相關資訊，Prometheus Server 再從其中拉取資料。 (圖片左半部)

2. Service Discovery 可以更好的蒐集 Kubernetes 相關的資訊。 (圖片上半部)

3. Prometheus Server 主體會將資料儲存在 Local On-Disk  Time Series Database 或是可以串接 Remote Storage Systems。(圖片下半部)

4. Prometheus Server 資料拉回來後可以提供本身自帶的 Web UI 或是 Grafana 等其他的 Client 來呈現。(圖片右下半部)

5. 當抓取資料的值超過 Alert Rule 所設定的閥值 (threshold) 時，Alertmanager 就會將訊息送出，可以透過 Email、Slack 等訊息通知，提醒相關人員進行處理。(圖片右上半部)

<br>

Prometheus 可能在儲存擴展上比不上其他 Time Series Database，但在整合各種第三方的 Data Source 上十分方便，且在支援雲端服務和 Container 容器相關工具也十分友好。但在圖片的表現上就相較於單薄，所以會搭配我們接下來要介紹的 Grafanac 精美儀表板工具來進行資訊視覺化和圖表的呈現。

<br>

{{< image src="/images/Prometheus-Grafana-Docker/grafana.jpg"  width="450" caption="Grafana Logo" src_s="/images/Prometheus-Grafana-Docker/grafana.jpg" src_l="/images/Prometheus-Grafana-Docker/grafana.jpg" >}}

<br>

Grafana 是由 Grafana Lab 經營的一個非常精美的儀表板系統，可以整合各種不同的 datasource，例如：Prometheus、Elasticsearch、MySQL、PostgreSQL等。透過不同種 metric 呈現在 dashboard 上。如果還是不太清楚，可以把 Prometheus Grafana 分別想成 Prometheus 是 EFK 的 Elasticsearch，Grafana 想成是 EFK 的 Kibana。

<br>

今天我們要透過 Docker-Compose 搭配 Nginx 實作一個簡單的 web service 範例，並整合 [Prometheus](https://prometheus.io/) 和 [Grafana](https://grafana.com/) 來建立一個 web service 監控預警系統。

此文章程式碼也會同步到 Github ，需要的也可以去查看歐！要記得先確定一下自己的版本 [Github 程式碼連結](https://github.com/880831ian/Prometheus-Grafana-Docker) 😆

<br>

## 版本資訊

* macOS：11.6
* Docker：Docker version 20.10.14, build a224086
* Nginx：[1.21.6](https://hub.docker.com/layers/nginx/library/nginx/1.21.6/images/sha256-b495f952df67472c3598b260f4b2e2ba9b5a8b0af837575cf4369c95c8d8a215?context=explore)
* Prometheus：[v.2.35.0](https://hub.docker.com/layers/prometheus/prom/prometheus/v2.35.0/images/sha256-4b86ad59abc67fa19a6e1618e936f3fd0f6ae13f49260da55a03eeca763a0fb5?context=explore)
* nginx-prometheus-exporter：[0.10](https://hub.docker.com/layers/nginx-prometheus-exporter/nginx/nginx-prometheus-exporter/0.10/images/sha256-f2aa9848516ff4c9f4c6d5bcb758b0fab519eff644f526e4c9a17d3083b54dde?context=explore)
* Grafana：[8.2.5](https://hub.docker.com/layers/grafana/grafana/grafana/8.2.5/images/sha256-1a154d1161ed65eaf87368d08149a8bbcf9962ac03dd5ff639a6b9d468a77a36?context=explore) (最新版本是 8.5.2，但選擇 8.2.5，是因為 8.3.0 後 Alerting 沒有辦法附上圖片，詳細原因可以參考 [Add "include image" option into Grafana Alerting](https://github.com/grafana/grafana/discussions/38030) )

<br>

## 檔案結構

```sh
.
├── docker-compose.yaml
├── nginx
│   ├── Dockerfile
│   └── status.conf
├── prometheus.yaml
└── test.sh
```

這是主要的結構，簡單說明一下：

* docker-compose.yaml：會放置要產生的 nginx、nginx-prometheus-exporter、prometheus、grafana 容器設定檔。
* nginx/Dockerfile：因為在 nginx 要使用 stub_status 需要多安裝一些設定，所以用 Dockerfile 另外寫 nginx 的映像檔。
* nginx/status.conf：nginx 的設定檔。
* prometheus.yaml：prometheus 的設定檔。
* test.sh：測試用	檔案(後續會教大家如何使用)。

<br>

## 實作

後續會依照執行的流程來跟大家說明歐！那要開始囉 😁

我們要建立一個 Nginx 來模擬受監控的服務，我們要透過 nginx-prometheus-exporter 來讓 Prometheus 抓到資料最後傳給 Grafana，所以我們在 Docker-compose 裡面會有 nginx、nginx-prometheus-exporter、prometheus、grafana 幾個容器，我們先看一下程式碼，再來說明程式碼再做什麼事情吧！

### Docker-compose.yaml

```yaml
version: '3.8'
services:
  nginx:
    build: ./nginx/
    container_name: nginx
    ports:
      - 8080:8080

  nginx-prometheus-exporter:
    image: nginx/nginx-prometheus-exporter:0.10
    container_name: nginx-prometheus-exporter
    command: -nginx.scrape-uri http://nginx:8080/stub_status
    ports:
      - 9113:9113
    depends_on:
      - nginx

  prometheus:
    image: prom/prometheus:v2.35.0
    container_name: prometheus
    volumes:
      - ./prometheus.yaml:/etc/prometheus/prometheus.yaml
      - ./prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yaml'
    ports:
      - '9090:9090'

  grafana:
    image: grafana/grafana:8.2.5
    container_name: grafana
    volumes:
      - ./grafana_data:/var/lib/grafana
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=pass
    depends_on:
      - prometheus
    ports:
      - '3000:3000'
```
* nginx：因為 Nginx 會通過 stub_status 頁面來開放對外的監控指標。所以我們要另外寫一個 Dockerfile 設定檔，先將 `conf` 放入 Nginx 中。
* nginx-prometheus-exporter：這裡要注意的是需要使用 command 來設定 nginx.scrapt-url，我們設定 `http://nginx:8080/stub_status`，他的預設 Port 是 `9113`，並設定依賴 `depends_no`，要 nginx 先啟動後才會執行 `nginx-prometheus-exporter`。
* prometheus：將 prometheus.yaml 設定檔放入 `/etc/prometheus/prometheus.yaml`，以及掛載一個 `/prometheus_data` 來永久保存 prometheus 的資料，以及 command 加入 `--config.file` 設定。
* grafana：一樣我們先掛載一個 `/grafana_data` 來永久保存 grafana 的設定，並設定依賴 `depends_on` ，最後設定 grafana 要呈現的畫面 Port。

<br>

### nginx/Dockerfile

```dockerfile
FROM nginx:1.21.6  
COPY ./status.conf /etc/nginx/conf.d/status.conf 
```
選擇我們要使用的 nginx image 版本，並將我們的設定檔，複製到容器內。

<br>

### nginx/status.conf

```conf
server {
    listen 8080;
    server_name  localhost;
    location /stub_status {
       stub_status on;
       access_log off;
    }
}
```
這邊最重要的就是要設定 `/stub_status` 路徑，開啟 stub_status ，這樣才可以讓 nginx-prometheus-exporter 抓到資料！

<br>

### prometheus.yaml

```yaml
global:
  scrape_interval: 5s # Server 抓取頻率
  external_labels:
    monitor: "my-monitor"
scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
  - job_name: "nginx_exporter"
    static_configs:
      - targets: ["nginx-prometheus-exporter:9113"]
```
這邊是 prometheus 的設定檔，例如有 scrape_interval Server 每次抓取資料的頻率，或是設定 monitor 的 labels，下面的 configs，分別設定了 prometheus 它的 targets 是 `["localhost:9090"]` 以及 nginx_exporter 它得 targets 是 `["nginx-prometheus-exporter:9113"]`。

<br>

### test.sh

```sh
#!/bin/bash

docker="docker exec nginx"

for i in {1..10}
do
	$docker curl http://nginx:8080/stub_status -s
done
```
這個是我自己另外寫的測試程式，可以執行後他會訪問容器內部 nginx，來模擬 nginx 流量，讓我們在 Grafana 可以清楚看到資料。

<br>

### 執行

<br>

### 測試

<br>

## 參考資料

[使用 Prometheus 和 Grafana 打造 Flask Web App 監控預警系統](https://blog.techbridge.cc/2019/08/26/how-to-use-prometheus-grafana-in-flask-app/)

[Nginx Exporter 接入](https://cloud.tencent.com/document/product/1416/56039)

[通過nginx-prometheus-exporter監控nginx指標](https://maxidea.gitbook.io/k8s-testing/prometheus-he-grafana-de-dan-ji-bian-pai/tong-guo-nginxprometheusexporter-jian-kong-nginx)

[使用nginx-prometheus-exporter 監控nginx](https://www.cnblogs.com/rongfengliang/p/13580534.html)