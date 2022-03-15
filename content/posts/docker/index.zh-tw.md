---
weight: 4
title: "Docker 介紹 (如何使用 Docker-compose 打包 php+mysql+nginx 環境)"
date: 2022-03-14T17:23:11+08:00
lastmod: 2022-03-14T17:23:11+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["Docker" , "容器" , "虛擬化"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

## 什麼是 Docker ?

Docker 是一種軟體平台，它可以快速建立、測試和部署應用程式。為什麼可以快速建立呢？因為 Docker 會將軟體封裝到『容器』的標準單位。其中包含了程式庫、系統工具、程式碼、執行軟體時所需的所有項目。
剛剛有提到容器，也就是 Container ，是一種虛擬化技術，它高效率虛擬化及易於遷移和擴展的特性，非常適合現代雲端的開發及佈署。那 Container 與傳統虛擬機有什麼差別呢？我們來看看下面這張圖

<br>

{{< image src="/images/docker/container-vm.png"  width="700" caption="Container 與 VM 的差異" src_s="/images/docker/container-vm.png" src_l="/images/docker/container-vm.png" >}}

可以看到 Container 是以應用程式為單位，而 VM 則是以作業系統為單位。Container 是一個封裝了相依性資源與應用程式的執行換行 ; VM 則是一個配置好 CPU、RAM 與 Storage 的作業系統，為了更好的做區別，我把 Container、VM 兩個差別用表格說明～

| 區別比較 | Container | VM |
| :---: | :---: | :---: |
| 單位 | 應用程式 | 作業系統 |
| 適用服務 | 多使用於微服務 | 使用較大型的服務 |
| 硬體資源 | 是以程式為單位，需要的硬體資源很少 | VM 會先佔用 CPU、RAM 等等硬體資源，不管有沒有使用都會先佔用 |
| 造成衝突 | Container 間是彼此隔離的，因此在同一台機器可以執行不同版本的服務 | 會因為版本不同造成環境衝突 |
| 系統支援量 | 單機支援上千個容器 | 一般都幾十個 | 
| 優點 | 1 . Image 較小，通常都幾MB<br>2 . 啟動速度快，通常幾秒就可以生成一個 Container<br>3 . 更新較為容易，只需要利用新的 Image 重新啟動就會更新了 |  1 . 因為硬體層以上都虛擬化，因此安全性相對較高<br>2 . 系統選擇較多，在 VM 可以選擇不同的OS<br>3 . 不需要降低應用程式內服務的耦合性，不需要將程式內的服務個別拆開來部署 |
| 缺點 | 1 . 安全性較 VM 差，因為環境與硬體都與本機共用<br>2 . 在同一台機器中，每一個 Container 的 OS 都是相同的，無法一個為 Windows、一個為 Linux，還是依賴 Host OS<br>3 . Container 通常會切成微服物的方式作部署，在各元件中的網路連結會比較複雜 | 1 . Image 的大小通常 GB 以上，比 Container 大很多<br>2 . 啟動速度通常要花幾分鐘，因此服務重啟速度較慢<br>3 . 資源使用較多，因為不只程式本身，還要將一部分資源分給 VM 的作業系統 |

**總結**

* 更快速的交付和部署：對於開發和維運人員來說，最希望就是一次建立或設定，可以再任意地方正常運行。開發者可以使用一個標準的映像檔來建立一套開發容器，開發完成之後，維運人員可以直接使用這個容器來部署程式碼。Docker 容器很輕很快！容器的啟動時間是秒級的，大量地節約開發、測試、部署的時間。

* 更有效率的虛擬化：Docker 容器的執行不需要額外的虛擬化支援，它是核心層級的虛擬化，因此可以實作更高的效能和效率。

* 更輕鬆的遷移和擴展：Docker 容器幾乎可以在任意的平台上執行，包括實體機器、虛擬機、公有雲、私有雲、個人電腦、伺服器等。 這種兼容性可以讓使用者把一個應用程式從一個平台直接遷移到另外一個。

* 更簡單的管理：使用 Docker，只需要小小的修改，就可以替代以往大量的更新工作。所有的修改都以增量的方式被分發和更新，從而實作自動化並且有效率的管理。

<br>

## Docker 基本概念

* 映像檔 (Image)
* 容器 (Container)
* 倉庫 (Repository)

這三個是 Docker 最基本的組成，了解後就可以知道整個 Docker 的生命週期。

### 映像檔 (Image)

Docker 映像檔是一個唯獨的模板。

例如：一個映像檔可在包含一個完整的 Linux 作業系統環境，裡面可以只安裝 Nginx 或使用者會使用到的其他應用程式。

我們會使用映像檔來建立 Docker 的容器 (Container) ，Docker 也提供很簡單的機制來建議映像檔或是更新現有的映像檔，也可以去下載別人已經做好的映像檔。

<br>

### 容器 (Container)

Docker 是利用容器來執行應用程式。

每一個容器都是由映像檔所建立的的執行程式。它可以被啟動、開始、停止、刪除。且每一個容器都是相互隔離的，不會相互影響。

<br>

### 倉庫 (Repository)

倉庫是集中放置映像檔的所在地，倉庫分為公開倉庫 (Public) 和 私有倉庫 (Private) 兩種形式。最大的倉庫註冊伺服器當然是  ([Docker hub](https://hub.docker.com/)) ，存放數量龐大的映像檔供使用者下載，使用者也可以在本地網路內建立一個私有倉庫。可以將倉庫的概念理解成跟 Git 相似。

{{< admonition tip "倉庫與倉庫註冊伺服器 (Registry) 的不同" ture >}}
倉庫註冊伺服器 (Registry) 裡面可以存放很多的倉庫，每一個倉庫又包含了很多的映像檔，每一個映像檔有不同的標籤 (tag) 。

舉個例子：Docker hub 是最大的倉庫註冊伺服器 (Registry) ，裡面又有很多不同的倉庫，像是 mariadb ，裡面又會包含很多的映像檔，以及不同的標籤 (tag)。
{{< /admonition >}}

<br>

## Docker 實作

本章節會介紹 Docker 三大組件映像檔 (image)、容器 (Container)、倉庫 (Repository) 是要如何實際操作，且他們的關聯性是什麼，可以先理解成，**我們先到 Docker hub 下載我們要用的映像檔 (Image)，然後啟動這些映像檔 (Image)，變成一堆的容器 (Container)，或是把我們想要的程式用 Dockerfile 打包成映像檔 (Image)，再上傳到倉庫 (Repository)內。**

<br>

### 映像檔 (Image)

本小節會介紹有關映像檔的內容，包括：

* 如何從倉庫取得映像檔
* 如何管理本地主機上的映像檔
* 映像檔實作

<br>

#### 如何從倉庫取得映像檔

我們可以先到 [Docker hub](https://hub.docker.com/) 上面看看有什麼服務或程式想要下載來做使用，找到想要的服務，我們可以下 `docker pull {要下載的服務、程式名稱}` ，<font color='green'>我們這邊就與我們這次要實作的主題一樣，先下載 PHP+Mysql+Nginx 這三個映像檔</font>。

* php
```sh
$ docker pull php
Using default tag: latest
latest: Pulling from library/php
f7a1c6dad281: Pull complete
.... 省略 ....
Digest: sha256:296a2346b33b4c561b07b48cd1c3d97c5a79dd5dcab92ec9f3bf1520d418f163
Status: Downloaded newer image for php:latest
docker.io/library/php:latest
```

<br>

* mysql
```sh
$ docker pull mysql
Using default tag: latest
latest: Pulling from library/mysql
15115158dd02: Pull complete
.... 省略 ....
Digest: sha256:b17a66b49277a68066559416cf44a185cfee538d0e16b5624781019bc716c122
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest
```

<br>

* nginx
```sh
$ docker pull nginx
Using default tag: latest
latest: Pulling from library/nginx
f7a1c6dad281: Already exists
4d3e1d15534c: Pull complete
.... 省略 ....
Digest: sha256:1c13bc6de5dfca749c377974146ac05256791ca2fe1979fc8e8278bf0121d285
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
```
由於我們下載的沒有加任何的 `tag` 也就是版本，所以我們都是下載最新版 latest ，如果想要下載特定版本，可以在服務名稱後面加上 `:版本` ，就可以下載對應的版本囉！

<br>

如果有標記 `Official Image` 就代表是官方釋出的映像檔 ~

<br>

{{< image src="/images/docker/docker-image.png"  width="900" caption="Docker hub 下載 Image" src_s="/images/docker/docker-image.png" src_l="/images/docker/docker-image.png" >}}

<br>

#### 管理本地主機上的映像檔

##### 查看映像檔 (images)

當我們下載好映像檔後，可以使用 `docker images` 來列出本機已下載的映像檔。

```sh
$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
php          latest    d6229b88aa29   3 days ago    484MB
mysql        latest    826efd84393b   6 days ago    521MB
nginx        latest    c919045c4c2b   13 days ago   142MB
```
<br>

我們來看看都列出哪些訊息吧！

* REPOSITORY：來自於哪一個倉庫，像是 php、mysql、nginx。
* TAG：映像檔的標籤，因為我們都下載最新版本所以是 latest。
* IMAGE ID：它的唯一 `ID` 號碼。
* CREATED：建立時間。
* VIRTUAL SIZE：映像檔大小。

<br>

##### 儲存映像檔 (save)

想要儲存映像檔，可以使用 `docker save {映像檔名稱} > {檔案名稱}.tar`，來做儲存。
```sh
$ docker save nginx > nginx.tar
$ ls | grep 'nginx'
nginx.tar
```


<br>

##### 刪除映像檔 (rmi)

想要刪除映像檔，可以使用 `docker rmi {映像檔名稱}` 來做刪除。
```sh 
$ docker rmi pinyi/demo-image
Untagged: pinyi/demo-image:latest
Deleted: sha256:1f56acbcbe9ec613a37e26934a84d98bed73879059f424dc69754520086baa37
```

* 注意！刪除映像檔(Image)時，要先用 `docker rm {容器}` 去刪除所有依賴這個映像檔的容器。

<br>

接著要怎麼建立自己的映像檔呢？我們要使用 Dockerfile 來建立映像檔。

#### 映像檔實作

##### 編輯 Dockerfile 映像檔

Dockerfile 是一種文字格式的設定檔，可以透過 Dockerfile 快速建立自訂的映像檔，換句話說，Dockerfile 就像是建置Docker Image 的腳本。

舉個例子：可以把自己想像成一位設計師，設計好房子的格局、擺飾等，畫好設計圖 (Dockerfile) 後，最後請師傅 (Docker) 依你的構思完成就可以了。

<br>

我們就來開始實作一個 Dockerfile 吧 ~

1. 我們先創建一個來放 Dockerfile 的資料夾，可以直接在路徑下建立出映像檔

```sh
mkdir demo-dockerfile
cd demo-dockerfile/
```

<br>

Dockerfile 結構，大致可以分為四個部分

* 基礎映像檔資訊
* 維護者資訊
* 映像檔操作指令
* 容器啟動時需執行的指令

<br>

2. 我們有說過 Dockerfile 是一個文字格式的設定檔，所以我們用 vim 來編寫 Dockerfile。

```sh
vim Dockerfile
```
<br>

```Dokcerfile
# 基礎映像檔資訊
FROM ubuntu:latest

# 維護者資訊
MAINTAINER ian

# 映像檔操作指令
RUN apt-get update -y\
    && apt-get install nginx -y

# 運行時容器提供服務的通道
EXPOSE 80

# 容器啟動時需執行的指令
CMD ["nginx","-g","daemon off;"]
```

<br>

`FROM ubuntu:latest` 

第一行為必要的指定基礎映像檔，這邊使用 ubuntu 作為基礎映像檔，我們用最新版本，所以是 latest。

<br>

`MAINTAINER ian` 

維護者資訊想不也是不可以少的，這邊也可以輸入 Email 資訊，只是要注意的是此資訊會寫入產出映像檔的Author名稱屬性中。

<br>

`RUN apt-get update -y\
    && apt-get install nginx -y` 

這邊是最重要的部分，想要在映像檔案上設定或安裝都需要將命令寫在這，格式必須依 RUN <command> ，RUN 指令後面放 Linux 指令，如果指令太長可以使用\來換行。 -y 是在安裝 Nginx，會同意所有進行中所出現的問題。

<br>

`EXPOSE 80` 

設定運行時容器提供服務的通道。

<br>

`CMD ["nginx","-g","daemon off;"]` 

最後就是啟動指定容器時預設執行的指令，格式是 CMD ["executable","param1","param2"]。

<br>

{{< admonition info "Docker 運行 nginx 時為什麼要使用 daemon off;" ture >}}
因為 Docker 容器啟動時，默認會把容器內部第一個進程，作爲 docker 容器是否正常運行的依據，如果 docker 容器 pid = 1 到進程掛了，docker 就會退出！

Docker 未執行自定義的 CMD 之前， nginx 的 pid 是 1，執行到 CMD 之後，nginx 就在後台運行，bash 或是 sh 的腳本就會變成 pid =1 。

所以一但執行完 CMD，nginx 容器就會退出了，所以才需要加上 `-g daemon off;`。

在 Nginx 官方的 [Docker Repository](https://hub.docker.com/_/nginx) 也有說明，在 Complex configuration 內。
{{< /admonition >}}

<br>

順便說一下使用 Dockerfile 的優點：1 . 可以進行 Git 版控，讓你管理或分享更方便 2 . 佔用容量小，因為只是純文字檔而已。

<br>

##### 使用 Dockerfile 建立映像檔

我們已經編輯完 Dockerfile 檔案了，接下來要執行來產生映像檔，我們要使用 `docker build` 來建立，我們一起來看看吧

```sh
docker build -t pinyi/demo-image . 
```

因為我們在 dockerfile 的目錄下，所以直接使用 “.” 來做建立動作，也可以使用 -f 來指定 dockerfile 的路徑位置。使用 -t 來設定映像檔的名稱，我們這邊取名叫 pinyi/demo-image。 

```sh
[+] Building 2.7s (7/7) FINISHED
 => [internal] load build definition from Dockerfile                                                                   0.0s
 => => transferring dockerfile: 44B                                                                                    0.0s
 => [internal] load .dockerignore                                                                                      0.0s
 => => transferring context: 2B                                                                                        0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                       2.6s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                          0.0s
 => [1/2] FROM docker.io/library/ubuntu:latest@sha256:8ae9bafbb64f63a50caab98fd3a5e37b3eb837a3e0780b78e5218e63193961f  0.0s
 => CACHED [2/2] RUN apt-get update -y    && apt-get install nginx -y                                                  0.0s
 => exporting to image                                                                                                 0.0s
 => => exporting layers                                                                                                0.0s
 => => writing image sha256:1f56acbcbe9ec613a37e26934a84d98bed73879059f424dc69754520086baa37                           0.0s
 => => naming to docker.io/pinyi/demo-image
 ```

<br>

完成後我們使用 `docker images` 來看看是否建立成功。

```sh
$ docker images
REPOSITORY         TAG       IMAGE ID       CREATED       SIZE
pinyi/demo-image   latest    1f56acbcbe9e   2 hours ago   166MB
php                latest    d6229b88aa29   4 days ago    484MB
mysql              latest    826efd84393b   6 days ago    521MB
nginx              latest    c919045c4c2b   13 days ago   142MB
```
執行運作的部分，我們放到容器 (Container)的章節在介紹 ~


<br>

### 容器 (Container)

我們在介紹指令前，先來了解一下 Dockerfile、Docker Image、Docker Container 這三個的關係，可以先參考以下圖片

{{< image src="/images/docker/container.png"  width="400" caption="容器 Container 組成" src_s="/images/docker/container.png" src_l="/images/docker/container.png" >}}

<br>

我們在啟動 Container 時，會有這三個部分組成，最底層是映像檔 (Image)，這一層主要是透過撰寫 Dockerfile 之後 build 出來的 Docker Image，就像我們前面說的它是一個唯獨的檔案。執行啟動了 Docker Container，就會加上第二層，就是需要先 Init Container 的設定，例如是 hostname、環境變數、網路連接等系統設定，最後最上層再加上使用者可以在此層去讀寫資料。

<br>

有關於容器 (Container) 的指令非常多，光是簡單的 run 就有很多參數，我們先列出比較常用且基本的 Container 指令～

#### Container 執行時的操作

##### 執行容器 (run)

我們想要創建一個新的容器並運行，就可以使用 `docker run`，我們來看看他可以使用哪些參數吧！

```sh
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

* OPTIONS 說明：


| 參數 | 描述 |
| :---: | :---: |
| -d | 後台運行容器 |
|  -i | 以交互模式運行容器，通常與 -t 同時使用 |
| -t | 為容器重新分配一個偽輸入終端，通常與 -i 同時使用 |
| -p | 指定容器與本機的Port ，格式是 `主機Port : 容器Port` |
| --name="{}" | 為容器設定名稱 | 
| --net="{}" | 指定容器的網路連接類型，支援 bridge/host/none/container 四種模式 |
| --link=[] | 添加連接到另一個容器 | 
| --volume,-v | 綁定一個本機的路徑 |

<br>

我們啟動我們下載好的 Nginx 來試試看吧！

```sh
$ docker run -d -p 7777:80 --name="demo-nginx" -v /Users/ian_zhuang/Desktop/data:/usr/share/nginx/html nginx
4f34a38d672a4e6c9063e48f0631739c2b4684e3fbad8d5d51b2da212c1aafa8
```
我們將 Nginx 容器在背景執行，且將預設的80 Port 與本機的7777 Port 綁在一起，讓我們在本機瀏覽7777 Port 會直接導向容器的80Port，設定容器的名字叫做 “demo-nginx" ，我們把桌面的 data 資料夾指向 Nginx 容器的路徑，我們就可以在本機新增檔案同步到容器中。

<br>

##### 顯示容器 (ps)

使用 `docker ps` 來檢查一下是否啟動成功 (`ps` 可以顯示映像檔的基本資訊，如果沒有加 -a 只會顯示執行中的容器)
```sh
$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS          PORTS                  NAMES
4f34a38d672a   nginx     "/docker-entrypoint.…"   About a minute ago   Up 59 seconds   0.0.0.0:7777->80/tcp   demo-nginx
```

<br>

接著我們測試是否有把桌面 data 資料夾掛到容器的路徑，我們先在 data 新增一個 hello.html ，裡面隨意輸入，瀏覽一下 `http://127.0.0.1:7777/hello.html` ，看看是否成功。

<br>

{{< image src="/images/docker/container-v.png"  width="700" caption="容器 Container -volume 測試" src_s="/images/docker/container-v.png" src_l="/images/docker/container-v.png" >}}

<br>

##### 顯示容器紀錄 (logs)

想要看到我們執行 Container 的紀錄，可以使用 `logs` 指令來顯示。
```sh
$ docker logs demo-nginx
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
... 省略 ...
2022/03/15 09:27:51 [notice] 1#1: start worker process 33
2022/03/15 09:27:51 [notice] 1#1: start worker process 34
2022/03/15 09:27:51 [notice] 1#1: start worker process 35
```

<br>

##### 刪除容器 (rm -f)

想要刪除不需要的 Container，可以使用 `rm` 指令來做刪除，`-f` 是強制刪除容器。
```sh
$ docker rm -f demo-nginx
demo-nginx
```

<br>

##### 進入容器 (exec)

想要進入 container 來查看資料或是修改檔案，可以使用 `exec` 來進入容器中。
```sh
$ docker exec -it demo-nginx /bin/bash
root@d5cf843e9781:/# ls
bin  boot  dev	docker-entrypoint.d  docker-entrypoint.sh  etc	home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@d5cf843e9781:/# cd usr/
bin/     games/   include/ lib/     libexec/ local/   sbin/    share/   src/
root@d5cf843e9781:/# cd usr/bin/
root@d5cf843e9781:/usr/bin# pwd
/usr/bin
```

<br>

##### 匯出檔案 (export)

我們前面有提到說，如果刪除了容器，以前寫入的資料也會不見，如果想要輸出資料，可以使用 `export` 來將可讀可寫那一層匯成檔案。
```sh
$ docker export demo-nginx > demo-nginx.tar
$ ls | grep 'demo'
demo-nginx.tar
```

<br>


{{< admonition tip "save 跟 export 的區別" ture >}}
還記得我們在儲存映像檔的時候有介紹到save嗎，那他跟 export的區別是什麼呢？我們可以理解成

save 是把 Docker Image 原始檔儲存，export 是把修改 Image 的內容都儲存。

{{< /admonition >}}

<br>

##### 匯入檔案 (import)

有匯出檔案，當然也有匯入檔案拉，可以使用 `import` 將我們匯出的檔案匯入 Docker Image 裡面。
```sh
$ cat ~/Desktop/demo-nginx.tar| docker import - import-nginx
sha256:7b8e3bd74b89b198b4b215ba5390535027c381c5683949bb6e0248ece7b7fe00

$ docker images
REPOSITORY     TAG       IMAGE ID       CREATED          SIZE
import-nginx   latest    7b8e3bd74b89   19 seconds ago   140MB
```

<br>

#### Container 的狀態

<br>

##### 檢查容器狀態 (inspect)

想要查看容器的狀態數據，可以使用 `inspect` 來顯示。
```sh
[
    {
        "Id": "e6690239ba28c60c8e98a647114b2d2d3e30008212f98f34c8b2f9ba1173ee9a",
        "Created": "2022-03-15T10:13:44.6165343Z",
        "Path": "/docker-entrypoint.sh",
        "Args": [
            "nginx",
            "-g",
            "daemon off;"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
.... 省略 ....
```

<br>

##### 查看容器的CPU、記憶體及網路使用 (stats)

想要查看容器的CPU、記憶體及網路使用，可以使用 `stats` 來顯示。
```sh
CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT     MEM %     NET I/O       BLOCK I/O     PIDS
e6690239ba28   test      0.00%     3.934MiB / 1.939GiB   0.20%     1.16kB / 0B   0B / 12.3kB   5
```

<br>

### 倉庫 (Repository)

<br>

## 參考資料
[Docker 官網](https://docs.docker.com/get-started/)

[什麼是 Docker？](https://aws.amazon.com/tw/docker/)

[Docker－－從入門到實踐](https://philipzheng.gitbook.io/docker_practice/#yuan-chu-chu-ji-can-kao-zi-liao)

[Dockerfile 建立自訂映像檔 — 架起網站快速又簡單（一）](https://medium.com/@jackercleaninglab/dockerfile-%E5%BB%BA%E7%AB%8B%E8%87%AA%E8%A8%82%E6%98%A0%E5%83%8F%E6%AA%94-%E6%9E%B6%E8%B5%B7%E7%B6%B2%E7%AB%99%E5%BF%AB%E9%80%9F%E5%8F%88%E7%B0%A1%E5%96%AE-%E4%B8%80-22b2743f97b9)

[用30天來介紹和使用 Docker](https://ithelp.ithome.com.tw/users/20103456/ironman/1320)