---
weight: 4
title: "Redis 介紹"
date: 2022-03-08T09:00:59+08:00
lastmod: 2022-03-08T14:27:53+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["Redis"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

## 什麼是 Redis ?

Redis 全名是 Remote Dictionary Server ，是快速的開源記憶體鍵值資料存放區 (keys-value database)。

由於 Redis 的回應時間極短，低於一毫秒，可以讓遊戲、金融服務、醫療保健等即時應用服務每秒處理幾百萬個請求。

### Redis 的優勢

#### 效能

所有的 Redis 資料都是存放在記憶體中，進而去實現低延遲和高傳輸量的資料存取。

#### 彈性的資料結構

一般的鍵值資料存放區提供的資料結構有限，而 Redis 提供多樣化的資料結構來滿足服務的需求，包含字串(Strings)、雜湊(Hash)、列表(List)、集合(Sets)、有序集合(Zset)。(後續會有詳細介紹)

#### 簡單易用

Redis 可以用更少、更精簡的指令來取代傳統複雜的程式碼，可以存取應用程式的資料。並支援 Java、Python、PHP、C/C++、C#、JavaScript、Node.js、Ruby、R、GO。

#### 複寫和持久性

Redis 主要採主副本架構，支援非同步複寫，可以將資料複製到多個伺服器。不但可以提升讀取效能(因為請求可分割到多部伺服器)，還可以再主服務器發生故障時快速恢復。至於持久性，Redis 支援時間點備份，會將資料複製到磁碟中。

<br>

## 實際操作

### 安裝 Redis

使用 Homebrew 來安裝 Redis  (Mac OS：11.6)
```sh
$ brew install redis
```

<br>
安裝好用 -v 檢查版本

```sh
$ redis-server -v
Redis server v=6.2.6 sha=00000000:0 malloc=libc bits=64 build=c6f3693d1aced7d9
$ redis-cli -v
redis-cli 6.2.6
```

還記得我們上面有說 Redis 會分成主副架構，一個是 Server、另一個是 Cli，所以在稍後測試時，需要開啟兩個 Terminal 來執行歐！

### 執行 Redis

#### Server

```sh
$ redis-server
39403:C 08 Mar 2022 11:13:17.500 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
39403:C 08 Mar 2022 11:13:17.500 # Redis version=6.2.6, bits=64, commit=00000000, modified=0, pid=39403, just started
39403:C 08 Mar 2022 11:13:17.500 
39403:M 08 Mar 2022 11:13:17.501 * Increased maximum number of open files to 10032 (it was originally set to 256).
39403:M 08 Mar 2022 11:13:17.501 * monotonic clock: POSIX clock_gettime
                _._
           _.-``__ ''-._
      _.-``    `.  `_.  ''-._           Redis 6.2.6 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 39403
  `-._    `-._  `-./  _.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |           https://redis.io
  `-._    `-._`-.__.-'_.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |
  `-._    `-._`-.__.-'_.-'    _.-'
      `-._    `-.__.-'    _.-'
          `-._        _.-'
              `-.__.-'

39403:M 08 Mar 2022 11:13:17.503 # Server initialized
39403:M 08 Mar 2022 11:13:17.503 * Ready to accept connections
```

如果出現上面符號，就代表 Server 已經啟動，接下來再開另一個 Terminal 來執行 Cli。

<br>

#### Cli 

開啟後，在下一個 `ping` 指令，該指令用於檢測 redis 服務是否啟動，正常會顯示 `pong`。
```sh
$ redis-cli
127.0.0.1:6379> ping
PONG
```
<br>

接下來，我們都會在 Cli 視窗做測試，會詳細介紹每一個資料型態以及其適合情境。

### 資料型態 

#### 字串 (Strings)

##### set、get
這是最基本的型態，一個 Strings 的欄位，最高可儲存 512 MB，這裡會用到的指令是 `set`、`get` ，分別用來儲存以及讀取字串，我們來看一下範例吧。

```sh
127.0.0.1:6379> set string hello-world
OK
127.0.0.1:6379> get string
"hello-world"
```
我們先用 `set` 將 hello-world 字串存到 string 這個 key，再用 `get` 顯示 string 裡面的值。

<br>

##### incr、decr
Redis 還有一些方便的指令，可以將字串轉成數字，做累加或累減。會使用到 `incr` 、`decr` ，分別代表累加，像是我們的 ++ ，以及累減，像是我們的 - -。 

```sh
127.0.0.1:6379> set num 10
OK
127.0.0.1:6379> incr num
(integer) 11
127.0.0.1:6379> incr num
(integer) 12
127.0.0.1:6379> decr num
(integer) 11
127.0.0.1:6379> decr num
(integer) 10
```
<br>

##### mset
我們也可以設定的時候，把要設定的值都一起設定，只需要使用 `mset` 就可以達成。

```sh
127.0.0.1:6379> mset 1 1 2 2
OK
127.0.0.1:6379> get 1
"1"
127.0.0.1:6379> get 2
"2"
127.0.0.1:6379> get 3
(nil)
```
如果用 `get` 顯示資料，若沒有對應的 key ，會顯示 (nil)。

<br>

#### 雜湊 (Hash)
可以把他想像成二維陣列，應該會比較好理解，我網路上找了一張圖，應該會比較清楚！

{{< image src="/images/redis/hash.png"  width="500" caption="Hash 示意圖 ([Redis 基本資料形態](https://blog.judysocute.com/2020/10/04/redis-%E5%9F%BA%E6%9C%AC%E8%B3%87%E6%96%99%E5%BD%A2%E6%85%8B/))" src_s="/images/redis/hash.png" src_l="/images/redis/hash.png" >}}

他的資料型態有像是，一個 user001 裡面是一個 Hash，Hash 裡面又會存放 name、phone、gender ，我們來實際操作看看。

```sh
127.0.0.1:6379> hset student name ian phone 0980123456 gender M
(integer) 3
127.0.0.1:6379> hget student name
"ian"
127.0.0.1:6379> hget student phone
"0980123456"
127.0.0.1:6379> hget student gender
"M"
```
Hash 的話要使用 `hset`、`hget` 來對 hash 做寫以及讀 。

<br>

#### 列表 (List)

這個也是很常見的 list 結構，這邊會使用到 `lpush`、`lrange` 來對 `List` 做讀寫。
```sh
127.0.0.1:6379> LPUSH list2 a a b b c d e
(integer) 7
127.0.0.1:6379> Lrange list2 0 10
1) "e"
2) "d"
3) "c"
4) "b"
5) "b"
6) "a"
7) "a"
```
<br>

#### 集合 (Sets)
其實跟 List 一樣，就是資料的集合，只是 `Sets` 多了一層限制，就是集合中的值不能重複，這邊會使用到 `sadd`、`smembers` 來對 `Sets` 做讀寫。

```sh
127.0.0.1:6379> sadd list3 a b c d a a b b
(integer) 4
127.0.0.1:6379> smembers list3
1) "c"
2) "a"
3) "b"
4) "d"
```
可以看到他實際寫入的只有 4 筆資料。

<br>

#### 有序集合 (Zset)

故名思義，有序集合就是有排序的集合，Sorted Sets 結構會 **多一個數字值去為排序的權重** 來決定先後順序，這邊會使用到 `zadd` 、 ` zrangebyscore` 來對 `Zset` 做讀寫。

```sh
127.0.0.1:6379> zadd sortset 10 '10'
(integer) 1
127.0.0.1:6379> zadd sortset 6 '6'
(integer) 1
127.0.0.1:6379> zadd sortset 2 '2'
(integer) 1
127.0.0.1:6379> zadd sortset 99 '99'
(integer) 1
127.0.0.1:6379> zrangebyscore sortset 0 100
1) "2"
2) "6"
3) "10"
4) "99"
```
就可以看出在顯示的時候並不是依照寫入順序，而是依照我們所設定的權重去排序的。
<br>

### 發布訂閱 (PUB/SUB)	

Redis 發布訂閱 (pub/sub) 是一種消息通信模式，發送者 (pub) 發送消息，訂閱者 (sub) 接收消息。

Redis 客戶端可以使用 `subscribe` 來訂閱任意數量的頻道。

下面這張圖是頻道1，以及訂閱這個頻道的三個用戶端分別是客戶端2、客戶端7、客戶端5

<br>

{{< image src="/images/redis/channel.png"  width="600" caption="Subscribe 示意圖" src_s="/images/redis/channel.png" src_l="/images/redis/channel.png" >}}

<br>

當我們有新消息通過 `publish` 指令發送給頻道1 ，這個消息就會被發送給有訂閱頻道1的三個客戶端。

<br>

{{< image src="/images/redis/message.png"  width="600" caption="Publish 示意圖" src_s="/images/redis/message.png" src_l="/images/redis/message.png" >}}

<br>

## 參考資料
[Redis 官網](https://redis.io)


[Redis](https://aws.amazon.com/tw/redis/)

[Redis 基本資料形態](https://blog.judysocute.com/2020/10/04/redis-%E5%9F%BA%E6%9C%AC%E8%B3%87%E6%96%99%E5%BD%A2%E6%85%8B/)

[Redis 發布訂閱](https://www.runoob.com/redis/redis-pub-sub.html)