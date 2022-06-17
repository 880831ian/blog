---
weight: 4
title: "Linux 常用指令"
date: 2022-06-17T15:26:00+08:00
lastmod: 2022-06-17T15:26:00+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["Linux", "指令", "介紹"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

因為最近在管理機器時，常常會使用各式各樣的指令來協助管理，所以把常用的指令依照不同類別整理在底下呦 😘 會定期更新該文章呦 ~

<br>

## 網路類

### 查詢遠端 Port 是否開放

#### nc (netcat)

nc (netcat)，可以讀取經過 TCP 及 UDP 的網路連線資料，是一套很實用的網路除錯工具。

<br>

安裝指令：

```
yum install nc
```

<br>

檢查 Port 是否有開放，可以用以下指令來查詢：

```
nc -zvw3 <IP:Port>
```
`-z` 只進行掃描，不進行任何的資料傳輸

`-v` 顯示掃描訊息

`-w3` 等待 3 秒

<br>

如果 Port 有開放，會回傳以下內容：

```
Ncat: Version 7.50 ( https://nmap.org/ncat )
Ncat: Connected to <IP:Port>.
Ncat: 0 bytes sent, 0 bytes received in 0.01 seconds.
```

<br>

如果沒有開放，會回傳以下內容：

```
Ncat: No route to host.
```

<br>

#### nmap

nmap (Network Mapper) 是另一個可以檢查 Port 的工具，安裝語法是這樣：

```
yum install nmap
```

<br>

檢查 Port 是否有開放，可以用以下指令來查詢：

```
nmap <IP> -p <Port>
```

<br>

如果 Port 有開放，會回傳以下內容：

```
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-17 16:06 CST
Nmap scan report for<Domain> (<IP>)
Host is up (0.0039s latency).

PORT   STATE SERVICE
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 1.09 seconds
```

<br>

如果沒有開放，會回傳以下內容：

```
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-17 16:06 CST
Nmap scan report for<Domain> (<IP>)
Host is up (0.0039s latency).

PORT   STATE SERVICE
80/tcp filtered  http

Nmap done: 1 IP address (1 host up) scanned in 1.09 seconds
```

<br>

#### Telnet

Telnet 也是一個可以檢查 Port 的工具，安裝語法是這樣：

```
yum install telnet
```

<br>

檢查 Port 是否有開放，可以用以下指令來查詢：

```
telnet <IP> <Port>
```

<br>

如果 Port 有開放，會回傳以下內容：

```
Trying <IP>…
Connected to <IP>.
Escape character is ‘^]’.
^CConnection closed by foreign host.
```

<br>

如果沒有開放，會回傳以下內容：

```
Trying <IP>…
telnet: Unable to connect to remote host: Connection refused
```

<br>

## 參考資料

[3 種檢查遠端埠號是否開啟的方法](https://www.ltsplus.com/linux/3-ways-check-remote-server-open-port)