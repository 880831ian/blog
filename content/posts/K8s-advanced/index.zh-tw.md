---
weight: 4
title: "Kubernetes (K8s) 介紹 - 進階 (Service、Ingress、Deployment、ReplicaSet)"
date: 2022-05-03T10:39:00+08:00
lastmod: 2022-05-03T10:39:00+08:00
draft: false
author: "PinYi"
authorLink: "https://pin-yi.me"
description: "此文章是介紹 Kubernetes 進階，會介紹 Service、Ingress、Deployment、ReplicaSet 等等，多數是參考網路大神分享，加上自己整理自己能理解的加以介紹，歡迎大家指教 😁"
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["Kubernetes", "K8s", "Docker", "介紹", "實作"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

前面我們在[基本篇](https://pin-yi.me/k8s)中，為了使 Pod 能夠與本機連線，使用了 `port-forward`，還有另一種方法就是今天要介紹的第一個主題：Service

那我們會先複習一下 `port-forward`，再來介紹 `Service`，當然後面也會有實際操作，請大家跟我繼續一起學下去吧 ！

<br>

## port-forward

`port-forward` 簡單來說就是把本機的某一個 Port 與 Pod 所開放對外的 Port 做映射，就像是我們在 Docker 跑 container 時會使用 -p 來連結機器與 container 的 port 一樣～

使用的方法也很簡單且方便，使用 `kubectl port-forward <pod> <external-port>:<pod-port>`，我們拿 [Kubernetes 基本篇](https://pin-yi.me/k8s)最後的範例，來做說明：

```sh
$ kubectl port-forward kubernetes-demo-pod 3000:3000

Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
Handling connection for 3000
```

我們把 kubernetes-demo-pod 這個 pod，用 port-forward 設定本機 3000 port 與 pod  3000 port 做映射，當我們瀏覽 `http://localhost:3000` 就可以看到 pod 裡面的內容了！

雖然很方便，可以馬上就開好要映射的 port，但缺點就是每次建立 pod 時都需要手動去打指令來設定 port，且時間久了，也會忘記本機上哪些 port 有被使用到，因此這邊推薦使用 `Service` 來取代 `port-forward`，那我們來看看 `Service` 是什麼吧。

<br>

## 什麼是 Service ?

`service` 他其實就是建立的一個網路連線通道，可以讓應用程式正確的連結到正在運行的 pods，而 `service` 又有4種的表現形式，我們接下來會一個一個簡單介紹：

### ClusterIP

它是 service 的預設值，所以沒有設定時，預設就是使用該方式做連線，它代表這個 service 只能在相同的 cluster 內使用，無法讓外部做使用。

<br>

### NodePort

簡單來說它可以從外部連線到內部使用。假設本機有其他服務，例如：nginx 之類的服務，還有架一個 K8s 的 cluster ，這時候只要設定好 NodePort，就可以讓本機使用 K8s cluster 來使用內部的服務。

<br>

### ExternalName

主要是為了讓不同 namespace ，以 ClusterIP 所生成的 service 可以利用 ExternalName 設定外部名稱，藉以連到指定的 namespace Service。

<br>

### LoadBalancer

這個屬性是強化版的 NodePort，除了 NodePort 可以讓外部連線的優點以外，同時也建立負載平衡的機制來分散流量，很可惜 LoadBalaner 只提供雲端服務，例如：GCP、AWS 等等都有支援，目前 minikube 要使用  LoadBalancer 需要先啟動 `tunnel` 才能做使用。`tunnel` 是什麼呢？我們後面會說明！

<br>

{{< image src="/images/K8s-advanced/service.png"  width="400" caption="k8s service 流程圖 [Kubernetes 那些事 — Service 篇](https://medium.com/andy-blog/kubernetes-%E9%82%A3%E4%BA%9B%E4%BA%8B-service-%E7%AF%87-d19d4c6e945f)" src_s="/images/K8s-advanced/service.png" src_l="/images/K8s-advanced/service.png" >}}

<br>

### NodePort 實作

那我們來用 Service (NodePort) 改寫[基本篇](https://pin-yi.me/k8s)的連線問題 ：([Github 程式碼連結](https://github.com/880831ian/kubernetes-tutorial/tree/master/Service-NodePort))

* service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: kubernetes-demo-service
  labels:
    app: demo
spec:
  type: NodePort
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
      nodePort: 30001
  selector:
    app: demo
```
結構與 kubernetes-demo.yaml 相同，以下簡單說明不同之處：

kind

該元件的屬性，此設定檔的類型是：Service

spec

* type：指定此 Service 要使用的方法，這邊我們使用 NodePort。
* ports.protocol：此為連線的網路協議，預設值為 `TCP`，當然也可以使用 `UDP`。
* ports.port：此為建立好的 Service 要以哪個 Port 連接到 Pod 上。
* ports.targetPort：此為目標 Pod 的 Port ，通常 port 跟 targetPort 一樣。
*  ports.nodePort：此為機器上的 Port 要對應到該 Service 上，這個設定要 nodePort 形式的 Service 才會有效果，假設今天沒有設定 nodePort ，Kubernetes 會自動開一個機器上的 Port 來對應該 Service ，範圍是在 30000 - 37267之間。
* selector.app：如果要使 Service 連接到正確的 Pod 就必須利用 `selector`，只要原封不動的把 Pod 的 `Labels` 複製上去即可。

<br>

接下來使用 `kubectl apply` 建立 service：

```sh
$ kubectl apply -f service.yaml
```

<br>

{{< image src="/images/K8s-advanced/service-4.png"  width="1300" caption="k8s 建立 service (NodePort)" src_s="/images/K8s-advanced/service-4.png" src_l="/images/K8s-advanced/service-4.png" >}}

<br>

接下來取得 minikube Node IP，可以使用：


```sh
$ minikube ip

192.168.64.11
```

<br>

打開瀏覽器搜尋 `192.168.64.11:30001` 就可以看到我們可愛的柴犬囉 ><

<br>

{{< image src="/images/K8s-advanced/Shiba-Inu-1.png"  width="1000" caption="成功顯示柴犬" src_s="/images/K8s-advanced/Shiba-Inu-1.png" src_l="/images/K8s-advanced/Shiba-Inu-1.png" >}}

<br>

### LoadBalancer 實作

那我們來用 Service (LoadBalancer) 改寫[基本篇](https://pin-yi.me/k8s)的連線問題 ：([Github 程式碼連結](https://github.com/880831ian/kubernetes-tutorial/tree/master/Service-LoadBalancer))

* service.yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: kubernetes-demo-service
  labels:
    app: demo
spec:
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
  selector:
    app: demo
```
結構與 kubernetes-demo.yaml 相同，以下簡單說明不同之處：

kind

該元件的屬性，此設定檔的類型是：Service

spec

* type：指定此 Service 要使用的方法，這邊我們使用 LoadBalancer。
* ports.protocol：此為連線的網路協議，預設值為 `TCP`，當然也可以使用 `UDP`。
* ports.port：此為建立好的 Service 要以哪個 Port 連接到 Pod 上。
* ports.targetPort：此為目標 Pod 的 Port ，通常 port 跟 targetPort 一樣。
* selector.app：如果要使 Service 連接到正確的 Pod 就必須利用 `selector`，只要原封不動的把 Pod 的 `Labels` 複製上去即可。

<br>

接下來使用 `kubectl apply` 建立 service：

```sh
$ kubectl apply -f service.yaml
```

<br>

{{< image src="/images/K8s-advanced/service-1.png"  width="1300" caption="k8s 建立 service (LoadBalancer)" src_s="/images/K8s-advanced/service-1.png" src_l="/images/K8s-advanced/service-1.png" >}}

可以看到 dashboard 有我們剛剛啟動的 Service，但是啟動後前面的燈是黃色的，是因為 minikube LoadBalancer 需要透過 `tunnel` 才可以使用，可以參考 minikube 官網說明：

<br>

{{< image src="/images/K8s-advanced/service-2.png"  width="800" caption="[minikube 官網](https://minikube.sigs.k8s.io/docs/handbook/accessing/#using-minikube-tunnel) 說明 LoadBalancer minikube tunnel" src_s="/images/K8s-advanced/service-2.png" src_l="/images/K8s-advanced/service-2.png" >}}

所以我們需要使用 `minikube tunnel` 來啟動 tunnel

<br>

```sh
$ minikube tunnel

✅  Tunnel successfully started

📌  NOTE: Please do not close this terminal as this process must stay alive for the tunnel to be accessible ...

🏃  Starting tunnel for service kubernetes-demo-service.
```

<br>

就可以看到燈號已經變成綠燈，在外部 Endpoints 多一個連結，可以直接點開

<br>

{{< image src="/images/K8s-advanced/service-3.png"  width="1300" caption="k8s service" src_s="/images/K8s-advanced/service-3.png" src_l="/images/K8s-advanced/service-3.png" >}}

<br>

就可以看到我們可愛的柴犬囉 ><

<br>

{{< image src="/images/K8s-advanced/Shiba-Inu.png"  width="1000" caption="成功顯示柴犬" src_s="/images/K8s-advanced/Shiba-Inu.png" src_l="/images/K8s-advanced/Shiba-Inu.png" >}}

<br>

## 什麼是 Ingress ?

還記得我們在 Service - NodePort 時，需要打 `<ip>:<port>` ，但現在網站除了網域以外，基本上不會需要自己去打 IP 以及 Port 了吧！那為了解決這個問題，有了 Ingreess。

Ingress 可以幫助我們統一對外的 port number，並根據 hostname 或是 pathname 來決定請求要轉發到哪一個 Service 上，之後就可以利用該 Service 連接到 Pod 來處理服務。

我們先來看一下一般的 Service ：

<br>

{{< image src="/images/K8s-advanced/describe-service.png"  width="800" caption="Service 圖片來源：[[Day 19] 在 Kubernetes 中實現負載平衡 - Ingress Controller](https://ithelp.ithome.com.tw/articles/10196261)" src_s="/images/K8s-advanced/describe-service.png" src_l="/images/K8s-advanced/describe-service.png" >}}

可以看到當多個 Service 同時運行時，Node 都需要有對應的 port number 去對應每個 Server 的 port number。像是 GCP 這種雲端服務，每台機器都會配置屬於自己的防火牆。這也代表，不論新增、刪除 Service 物件，都必須要額外多調整防火牆的設定，Port 的管理也想對複雜。

<br>

若是使用 Ingress，我們只需要開放一個對外的 port numer，Ingree 可以在設定檔中設定不同的路徑，決定要將使用者的請求傳送到哪一個 Service 物件上：

{{< image src="/images/K8s-advanced/describe-ingress.png"  width="900" caption="Ingress 圖片來源：[[Day 19] 在 Kubernetes 中實現負載平衡 - Ingress Controller](https://ithelp.ithome.com.tw/articles/10196261)" src_s="/images/K8s-advanced/describe-ingress.png" src_l="/images/K8s-advanced/describe-ingress.png" >}}

這樣的設計，除了讓維運人員不需要維護多個 port 或是頻繁的更改防火牆外，可以自訂條件的功能，也使得請求的導向可以更加彈性。

<br>
	
### Ingress 功能

* 將不同"路徑"的請求對應到不同的 Service 物件

若沒有設定網域，則該機器上的所有網域只要透過此路徑都可以連接到指定的 Service 物件。

<br>

* 將不同"網域"的請求對應到不同的 Service 物件

若沒有設定路徑，則會以 `/` 路徑連接到指定的 Service 物件。

<br>

* 支援 SSL Termination

SSL 全名是傳輸層安全性協定，而網站通常都會利用 https 進行加密以確保資料安全，但 Service 與 Pod 之間的溝通都是以無加密方式傳輸，所以 Ingress 就支援解密，讓 Service 與 Pod 可以正常溝通傳遞資料。

<br>

### minikube 啟動 Ingress

由於 minikube 預設沒有啟動 Ingress 功能，因此需要額外使用 `minikube addons enable ingress` 讓 minikube 啟動 Ingress (Ingress 也需要先安裝 Hyperfix)：

<br>

{{< image src="/images/K8s-advanced/ingress.png"  width="900" caption="Ingress 圖片來源：[[Day 19] 在 Kubernetes 中實現負載平衡 - Ingress Controller](https://ithelp.ithome.com.tw/articles/10196261)" src_s="/images/K8s-advanced/ingress.png" src_l="/images/K8s-advanced/ingress.png" >}}

<br>

### 設定 /etc/hosts

加入 Ingress 基本上就需要網域才可以使用，但我們在本機上做練習，所以只要修改本機的 host 檔案就可以了(加入 minikube ip 以及想要的網域名稱)。

```sh
$ vim /etc/hosts

192.168.64.11 test.tw
192.168.64.11 test-test.tw
```

<br>

因為有兩種方法，第一種 (設定網域以及路徑)跟第二種 (只設定路徑沒有設定網域)，所以底下也會分成兩種來說明！

### 設定網域以及路徑

[Github 程式碼連結](https://github.com/880831ian/kubernetes-tutorial/tree/master/Ingress-Domain)

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: kubernetes-demo-ingress
spec:
  rules:
    - host: "test.tw"
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: kubernetes-demo-service
                port:
                  number: 3000
```

<br>

### 只設定路徑沒有設定網域

[Github 程式碼連結](https://github.com/880831ian/kubernetes-tutorial/tree/master/Ingress-Path)

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: kubernetes-demo-ingress
spec:
  rules:
    - http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: kubernetes-demo-service
                port:
                  number: 3000
```

<br>

Ingress 整體寫法與 Service 差不多，只差在 `spec` 的細部設定，我們就來說一下 spec 設定吧：

對了～ Ingress 的 apiVersion 不像是 Pod 跟 Service 一樣使用 `v1` ，因為目前支援 Ingress 的 API 只有 networking.k8s.io/v1，詳細可以參考 [kubernetes Ingress 官網](https://kubernetes.io/docs/concepts/services-networking/ingress/)

* rules

這代表這個 Ingress 的轉發規則，此 Ingress 所有的設定都必須寫載 `rules` 內。

* host

設定可以連接到 Service 物件的網路名稱。

* path

設定可以連接到 Service 物件的路徑名稱。

* pathType

分為 `Prefix` 和 `Exact` 兩種，Prefix：前綴符合就符合規則 ; Exact：需要完全一致才行，包含大小寫。

| type | path | request path | macth | 
| :---: | :---: | :---: | :---: |
| Prefix | / | 全部路徑 | Yes |
| Exact | /aa | /aa | Yes | 
| Exact | /bb | /cc | No | 
| Prefix | /aa | /aa,/aa/ | Yes |
| Prefix | /aa/cc | /aa/ccc | No |

* service.name

設定連接到的 Service 名稱，這裡要填寫的就是 Service 中在 `metadata` 內寫的 `name`。

* service.port.number

設定要經由哪個 port 連接到 Service 物件，就像是 Service 的 Port 要連接到 Pod 的 targetPort。

<br>

### 建立 Ingress 

一樣我們分成兩個做說明

#### 設定網域以及路徑

一樣使用 `apply` 來建立：

```
$ kubectl apply -f ingress.yaml 

ingress.networking.k8s.io/kubernetes-demo-ingress created
```

<br> 

使用 `kubectl get ing` 來查詢 Ingress 狀況：
```
$ kubectl  get ing

NAME                      CLASS   HOSTS     ADDRESS         PORTS   AGE
kubernetes-demo-ingress   nginx   test.tw   192.168.64.11   80      10m
```

接下來我們分別測試寫在 /etc/host 裡面的兩個網域，使用瀏覽器搜尋 `test.tw` 跟 `test-test.tw`：


<br>

* `test.tw`
{{< image src="/images/K8s-advanced/Shiba-Inu-3.png"  width="1000" caption="成功顯示柴犬" src_s="/images/K8s-advanced/Shiba-Inu-3.png" src_l="/images/K8s-advanced/Shiba-Inu-3.png" >}}

<br>

* `test-test.tw `
{{< image src="/images/K8s-advanced/Shiba-Inu-4.png"  width="800" caption="顯示 404" src_s="/images/K8s-advanced/Shiba-Inu-4.png" src_l="/images/K8s-advanced/Shiba-Inu-4.png" >}}

<br>

從上面結果可以知道，因為我們有設定 domain，所以只有符合的才會連接到 Service 。

<br>

#### 只設定路徑沒有設定網域

一樣使用 `apply` 來建立：

```
$ kubectl apply -f ingress.yaml 

ingress.networking.k8s.io/kubernetes-demo-ingress created
```

<br> 

使用 `kubectl get ing` 來查詢 Ingress 狀況：
```
$ kubectl  get ing

NAME                      CLASS   HOSTS   ADDRESS         PORTS   AGE
kubernetes-demo-ingress   nginx   *       192.168.64.11   80      5m56s
```

接下來我們分別測試寫在 /etc/host 裡面的兩個網域，使用瀏覽器搜尋 `test.tw` 跟 `test-test.tw`：


<br>

* `test.tw`
{{< image src="/images/K8s-advanced/Shiba-Inu-3.png"  width="1000" caption="成功顯示柴犬" src_s="/images/K8s-advanced/Shiba-Inu-3.png" src_l="/images/K8s-advanced/Shiba-Inu-3.png" >}}

<br>

* `test-test.tw `
{{< image src="/images/K8s-advanced/Shiba-Inu-5.png"  width="1000" caption="顯示 404" src_s="/images/K8s-advanced/Shiba-Inu-5.png" src_l="/images/K8s-advanced/Shiba-Inu-5.png" >}}

<br>

從上面結果可以知道，可以發現 hosts 的部分是 `*` ，這代表所有背後指向 `192.168.64.11` 這個 IP 的網域都可以直接連接到 Service 物件。

<br>

## 參考資料

[Kubernetes 那些事 — Service 篇](https://medium.com/andy-blog/kubernetes-%E9%82%A3%E4%BA%9B%E4%BA%8B-service-%E7%AF%87-d19d4c6e945f)

[Kubernetes 那些事 — Ingress 篇（一）](https://medium.com/andy-blog/kubernetes-%E9%82%A3%E4%BA%9B%E4%BA%8B-ingress-%E7%AF%87-%E4%B8%80-92944d4bf97d)

[Kubernetes 那些事 — Ingress 篇（二）](https://medium.com/andy-blog/kubernetes-%E9%82%A3%E4%BA%9B%E4%BA%8B-ingress-%E7%AF%87-%E4%BA%8C-559c7a41404b)