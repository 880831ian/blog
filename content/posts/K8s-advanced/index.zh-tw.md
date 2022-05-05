---
weight: 4
title: "Kubernetes (K8s) 介紹 - 進階 (Service、Ingress、StatefulSet、Deployment、ReplicaSet)"
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

{{< image src="/images/K8s-advanced/service.png"  width="300" caption="k8s service 流程圖 [Kubernetes 那些事 — Service 篇](https://medium.com/andy-blog/kubernetes-%E9%82%A3%E4%BA%9B%E4%BA%8B-service-%E7%AF%87-d19d4c6e945f)" src_s="/images/K8s-advanced/service.png" src_l="/images/K8s-advanced/service.png" >}}

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

接下來我們要進入到 Pod 的擴充功能，再進入之前，先帶大家了解一下兩個重要的觀念：

## 什麼是 Stateless 和 Stateful

### Stateless

Stateless 顧名思義就是無狀態，我們可以想成我們每次與伺服器要資料的過程中都不會被伺服器記錄狀態，**每一次的 Request 都是獨立的**，彼此是沒有關聯性的，也就是我們當下獲得的資料只能當下使用沒有辦法保存，靜態網頁通常都是一種 Stateless 的應用。

舉個例子來說：今天我想要查詢火車時刻表，我可以藉由 Google 搜尋火車時刻表，並點選連結，或是直接在瀏覽器輸入 `https://tip.railway.gov.tw/` ，這兩種結果最後都會一致，並不會因為我的操作不同而產生不同結果，這就是一種 Stateless 的表現。

<br>

### Stateful

Stateful 就是 Stateless 的相反，也就是每次的 Request 都會被記錄下來，日後都可以進行存取，Stateful 最常見的例子是資料庫，所以我們可以理解成 Stateful 背後一定會有一個負責更新內容的儲存空間。

幾個例子來說：今天我們想要查看 Google 雲端硬碟的內容，我們必須先登入自己的帳號才可以查看內容，這種有操作先後順序才會有結果的就是一種 Stateful 的表現。

<br>

{{< image src="/images/K8s-advanced/stateless_ful.png"  width="700" caption="Stateless vs Stateful" src_s="/images/K8s-advanced/stateless_ful.png" src_l="/images/K8s-advanced/stateless_ful.png" >}}

<br>

那為什麼要提到 Stateless 跟 Stateful 呢？

因為跟 Pod 有很大的關係，在 Kubernetes 中 Pod 就屬於 Stateless 的，我們前面有提到 Stateless 的特性就是每次的 Request 都是獨立的，這樣有一個好處是可以快速的擴充。

在 [Kubernetes - 基本篇中的 Pod](https://pin-yi.me/k8s/#pod) 有提到：Pod 是 Kubernetes 中最小的單位，由於 Pod 是屬於 Stateless 的，即便今天同一種內容的 Pod 有很多個也沒有關係，因為每次的 Request 都是獨立的，多個 Pod 就多個連線的端點而已。

<br>

### Kubernetes 的 Stateful

上面有說到 Kubernetes 的 Pod 是 Stateless 的，那難道 Kubernetes 沒有辦法做 Stateful 應用嗎？其實是可以的，Kubernetes 為了 Stateful 有特別開啟一個類別叫：`StatefulSet`

<br>

這邊會簡單說明一下 `StatefulSet` 的架構：

### StatefulSet

StatefulSet 一共有兩個重要的部分：

* Persistent Volume Claim

前面有說到 Stateful 背後有一個更新內容的儲存空間，在 Kubernetes 中負責管理儲存的空件是 `Volume`，作用與 Docker 的 Volume 幾乎一模一樣，但 Kuberntes 的 Volume 只是在 Pod 中暫時存放的儲存空間，當 Pod 移除之後這個儲存空間就會消失，為了要在 Kubernetes 中建立一個像是資料庫可以永久儲存的空間，這個 Volume 不能被包含在 Pod 中，而這個就是 `Persistent Volume (PV)`。

Persistent Volume Claim (PVC) 就是負責連接 Persistent Volume (PV) 的物件，所以可以想像一下今天有多少的 Persistent Volume 就會有多少的 Persistent Volume Ckaim。

<br>

* Headless Service

還記得在 [Service](#什麼是-service-) 有提到 ClusterIP 嗎？其實每個 Service 都會有自己一組的 ClusterIP (ExternalName 形式的除外)，所以 Headless 的意思其實就是不要有 ClusterIP，方法也很簡單，直接在設定檔中加入 `ClusterIP: Nono` 就可以了！

這麼做有什麼好處？由於 Headless Service 並沒有直接跟 Pod 有對應關係，因此 Service 本身沒有 ClusterIP，所以 Kubernetes 內部在溝通時就沒有辦法把我們設定好的 Service 名稱進行 IP 轉換，不過 Headless Service 會將內部的 Pod 的都建立屬於自己的 domain，所以我們可以自由的選擇要連接到哪一個 Pod。

這時候你會說可以用手動來連接呀？但因為 Service 一般是跟著 Pod 的 Label ，所以一個 Service 都會連接許多個 Pod，這樣我們就沒有辦法針對某個 Pod 來做事情，所以 Headless Service 在 Stateful 中也會被建立。

<br>

我們一直強調說 Kubernetes 最小的單位是 Pod，即便是 StatefulSet 也會有 Pod，只是這個 Pod 會歸 StatefulSet 管理，綜合上面所述可以知道一個 StatefulSet 裡面除了執行的 Pod 外還會有負責跟 Persistent Volume 連接的 Persistent Volume Claim，整體的 StatefulSet 架構會長得像這樣：

<br>

{{< image src="/images/K8s-advanced/StatefulSet.png"  width="300" caption="Kubernetes StatefulSet 架構" src_s="/images/K8s-advanced/StatefulSet.png" src_l="/images/K8s-advanced/StatefulSet.png" >}}

<br>

前面有提到 Pod 是 Stateless，所以我們可以擴充 Pod，今天這邊文章要開始介紹如何擴充 Pod ，我們從最簡單的 [Replication Controller](https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/) 開始說起！

## 什麼是 Replication Controller ?

大家看到 Controller 就知道 Replication Controller 也是一種 Controller 負責控制 Replication，而 Replication 翻成中文是複製的意思，在 Kubernetes 中 Replication 代表同一種 Pod 的複製品。

這邊要帶給大家認識一個重要的設定：`replica`，`replica` 就是複製品的意思，透過這個設定我們就可以快速產生一樣內容的 Pod，舉例來說：今天設定了 `replica: 3` 就代表會產生兩個內容一樣的 Pod 出來。

<br>

{{< image src="/images/K8s-advanced/ReplicationController.png"  width="700" caption="Kubernetes StatefulSet 架構" src_s="/images/K8s-advanced/ReplicationController.png" src_l="/images/K8s-advanced/ReplicationController.png" >}}

<br>

### Replication Controller 用途

上面有提到 Replication Controller 可以利用設定 `replica` 的方式快速建立 Pod 數量，除了建立之外 Replication Controller 也確保 Pod 數量與我們設定的 `replica` 一致，假如今天不小心刪除其中一個 Pod，這時候 Replication Controller 會自動再產生一個新的 Pod 來補齊刪除的 Pod 空缺，所以我們可以善用 Replication Controller 來讓系統更佳穩定。

<br>

### Replication Controller 寫法

我們來修改一下之前的 kubernetes-demo.yaml Pod 檔案：

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: kubernetes-demo
spec:
  replicas: 3
  selector:
    app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
        - name: kubernetes-demo-container
          image: 880831ian/kubernetes-demo
          ports:
            - containerPort: 3000
```

這個設定檔看似複雜但其實很簡單，可以發現 `template` 區塊內的設定基本上就是 Pod 的設定，再加上一些屬於 Replication Controller 的設定。

由於我們要建立的是 Replication Controller，因此在一開始的 `spec` 要填的是 Replication Controller 的設定，所以 `replica` 會擺在第一個 `spec` 內。

可以再看到 `selector`，前面提到 Replication Controller 要控制的就是 Pod 的數量，所以這邊的 `selector` 就是要選取 Pod，就跟我們在 Service 要選取 Pod 的一樣。

最後一個新的設定：`template`，`template` 就是用來定義 Pod 的資訊，所以 Pod 的內容像是 `metadata`、`spec` 等等都會寫在 `template` 內，所以可以把 `template` 想像成不需要寫 `apiVersion` 跟  `kind` 的 Pod 壓模檔，有了這個觀念再來看 `template` 內的描述就很簡單，只是把 Pod 的內容複製過來而已，而 `template` 內的 `spec` 就是寫上 Pod 的 container 資訊。

<br>

### Replication Controller 建立

老樣子，也是使用 `apply` 來建立壓模檔：

```sh
$ kubectl apply -f kubernetes-demo.yaml

replicationcontroller/kubernetes-demo created
```	

<br>

來查看一下 Replication Controller 是否有成功建立起來，可以使用 `kubectl get rc` 來查詢：

```sh
$ kubectl get rc

NAME              DESIRED   CURRENT   READY   AGE
kubernetes-demo   3         3         3       28s
```

<br>

接下來可以查看 Pod 是否有出現 3 個，所以使用 `kubectl get po` 來查詢：

```sh
$ kubectl get po

NAME                    READY   STATUS    RESTARTS   AGE
kubernetes-demo-4zkxm   1/1     Running   0          37s
kubernetes-demo-cp8jt   1/1     Running   0          37s
kubernetes-demo-pt9px   1/1     Running   0          37s
```
可以看到為了不要讓名稱重複，所以 Replication Controller 會在每一個 Pod 名稱後面加入亂數。

<br>

接下來我們用 `minikube dashboard` 來測試一下，是否刪除其中一個 Pod 後，Replication Controller 會自動建立新的：


<br>

{{< image src="/images/K8s-advanced/rc-1.png"  width="1200" caption="刪除隨機一個 Pod" src_s="/images/K8s-advanced/rc-1.png" src_l="/images/K8s-advanced/rc-1.png" >}}

<br>

當我們隨機刪除一個 Pod 時，被刪除的 Pod 會 Terminating 準備刪除，且啟動一個新的 Pod ContainerCreating：

{{< image src="/images/K8s-advanced/rc-2.png"  width="1200" caption="Pod 服務" src_s="/images/K8s-advanced/rc-2.png" src_l="/images/K8s-advanced/rc-2.png" >}}

<br>

當新的 Pod 啟動成功後，舊的 Pod 才會被刪除，所以可以確保我們的服務穩定度。

{{< image src="/images/K8s-advanced/rc-3.png"  width="1200" caption="Pod 服務" src_s="/images/K8s-advanced/rc-3.png" src_l="/images/K8s-advanced/rc-3.png" >}}

<br>

綜上所述，可以知道 Replication Controller 真的會控制 Pod 數量，那我們刪掉一個 Pod 他就重生一個，這樣不會永遠都刪不完嗎？其實我們可以把 Replication Controller 砍掉就好了，而 Replication Controller 刪除時，也會自動終止底下的 Pod ，最後 Pod 都會自動刪除。

但其實 Kubernetes 官方不建議使用 Replication Controller 的方式來控制 Pod，而是建議使用 Deployment 搭配 ReplicaSet 來控制，我們接下來要介紹的主題就是：`Deployment` 跟 `ReplicaSet` 。

<br>

## 參考資料

[Kubernetes 那些事 — Service 篇](https://medium.com/andy-blog/kubernetes-%E9%82%A3%E4%BA%9B%E4%BA%8B-service-%E7%AF%87-d19d4c6e945f)

[Kubernetes 那些事 — Ingress 篇（一）](https://medium.com/andy-blog/kubernetes-%E9%82%A3%E4%BA%9B%E4%BA%8B-ingress-%E7%AF%87-%E4%B8%80-92944d4bf97d)

[Kubernetes 那些事 — Ingress 篇（二）](https://medium.com/andy-blog/kubernetes-%E9%82%A3%E4%BA%9B%E4%BA%8B-ingress-%E7%AF%87-%E4%BA%8C-559c7a41404b)

[Kubernetes 那些事 — Stateless 與Stateful](https://medium.com/andy-blog/kubernetes-%E9%82%A3%E4%BA%9B%E4%BA%8B-stateless-%E8%88%87stateful-2c68cebdd635)

[kubernetes ReplicationController](https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/)

[Kubernetes 那些事 — Replication Controller](https://medium.com/andy-blog/kubernetes-%E9%82%A3%E4%BA%9B%E4%BA%8B-replication-controller-5c8592d37083)
