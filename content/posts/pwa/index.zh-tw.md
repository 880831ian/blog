---
weight: 4
title: "PWA(Progressive Web App) 介紹與實際安裝"
date: 2022-02-19T19:34:11+08:00
lastmod: 2022-02-19T19:34:11+08:00
draft: false
author: "PinYi"
authorLink: ""
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["PWA", "設定教學"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

PWA 全名是 Progressive Web App，也就是漸進式的網站應用程式，逐步的將網站漸進優化為具備 APP 的優點，其有以下幾點特性。


<!--more-->

## 介紹

PWA 全名是 Progressive Web App，也就是漸進式的網站應用程式，逐步的將網站漸進優化為具備 APP 的優點，其有以下幾點特性。

* Progressive
	* 漸進式，提供每一位用戶做基本的瀏覽。
* Responsive
	* 響應式的用戶介面，可以在不同裝置下作最佳化的顯示。
*	Connectivity independent
	* 不依賴網路連接，透過 service workers 可以在低頻寬甚至是離線的環境下瀏覽網站。
* App-like
	* 讓網站可以具有像 APP 般的瀏覽速度等優點，提供更佳的用戶體驗。
* Fresh
	* 藉由 service worker 自動更新網站內容。
* Installable
	* 可以藉由 Add To Home，如同 App，會新增一個 icon，可以直接將網站加到手機桌面上做切換使用，不需要再透過 App Store 下載安裝。

詳細可參考[官方網站](https://developer.mozilla.org/zh-TW/docs/Web/Progressive_web_apps)


## 實際安裝
{{< admonition tip "範例下載位置" ture >}}
先下載 https://nas.pin-yi.me/sharing/nElXqbqyt (放置小弟NAS雲端上)
內有兩個檔案，分別是manifest.json、service-worker.js，以下各別做介紹
{{< /admonition >}}

### manifest.json (簡易介紹，詳細可參考[官方文件](https://developer.mozilla.org/zh-TW/docs/Mozilla/Add-ons/WebExtensions/manifest.json))

```json
{
          "background_color": "#fff", 
          "display": "standalone", 
          "orientation":"portrait",
          "theme_color": "#fff",
          "short_name": "縮短名稱",
          "name": "名稱",
          "description": "說明",
          "lang": "zh-TW",
          "icons": [
          {
            "src": "images/logo/logo180.ico",
            "sizes": "180x180",
            "type": "image/png"
          },
          {
            "src": "images/logo/logo48.ico",
            "sizes": "48x48",
            "type": "image/png"
          }
          ],
          "start_url": "./index.php"
}
```

* background_color 背景顏色
	* 定義 Web 應用程式預期的背景顏色，這能在 Web 應用程式的啟動和載入內容之間創建平順的過場。
	
``` json
	"background_color": "#fff"
```

* display 背景顏色
	* 定義開發者喜好的 Web 應用程式顯示模式。

``` json
	"display": "standalone"
```
	
|  顯示模式   | 描述  |
|  ----  | ----  |
| fullscreen  | 所有可用的顯示區域都被填充並且不顯示使用者代理 chrome 。 |
| standalone	  | 這看起來和感覺上就像是獨立應用程式一樣，包括有不同的執行視窗、有圖示的應用程式啟動器 … 等等。 在這模式下，使用者代理將不包含控制導覽列，但能包含其他的 UI 元素，像是狀態列。 |
| minimal-ui	| 這看起來和感覺上就像是獨立應用程式一樣，但將有控制導覽列 UI 元素的最小設置，元素會因瀏覽器而不同。|
| browser | 預設值。 應用程式如常規般地被開啟於瀏覽器分頁或新視窗，依瀏覽器與平台而不同。|

* orientation 螢幕顯示方向
	* 定義預設的顯示方向，通常應用在 GAME 裡，可能會需要強制設定方向。
```json
"orientation": "portrait"
```
* theme_color 網站佈景顏色
	* 設定網站每個頁面的主題顏色，例如改變 URL 的顏色。

```json
"theme_color": "#fff"
```

* short_name 縮短名稱
	* 定義 Web 應用程式的縮短名稱。

```json
"short_name": "縮短名稱"
```

* name 名稱
	* 定義 Web 應用程式的名稱。

```json
"name": "名稱"
```

* name 名稱
	* 定義 Web 應用程式的名稱。

```json
"name": "名稱"
```

* description 說明
	* 提供一段描述來形容這個 Web 應用程式的作用是什麼。

```json
"description": "說明"
```

* lang 語言
	* 定義 Web 應用程式的語言。

```json
"lang": "zh-TW"
```

* icons 圖示
	* 應用程式圖示的物件。

```json
 "icons": [
          {
            "src": "images/logo/logo180.ico",
            "sizes": "180x180",
            "type": "image/png"
          },
          {
            "src": "images/logo/logo48.ico",
            "sizes": "48x48",
            "type": "image/png"
          }
          ]
```

* start_url 開始網址
	* 定義 Web 應用程式的開始位置。

```json
 "start_url": "./index.php"
```

* service-worker.js

```js
// 當service worker在「安裝階段」時會觸發此事件
self.addEventListener('install', function(event) {
    console.log('[Service Worker] Installing Service Worker ...', event);
});

// 當service worker在「激活階段」時會觸發此事件
self.addEventListener('activate', function(event) {
    console.log('[Service Worker] Activating Service Worker ...', event);
    return self.clients.claim();   // 加上這行是為了確保service worker被正確載入和激活，不加也行
});
self.addEventListener('fetch', function(event) {
    console.log('[Service Worker] Fetch something ...', event);
    event.respondWith(fetch(event.request));
});
let deferredPrompt;

self.addEventListener('beforeinstallprompt', function(event) {
    console.log('beforeinstallprompt fired');
    event.preventDefault();  // 取消預設的直接跳出通知設定
    deferredPrompt = event;  // 將監聽到的install banner事件傳到deferredPrompt變數

    return false;
});
if(deferredPrompt) {   // 確定我們有「攔截」到chrome所發出的install banner事件
    deferredPrompt.prompt();   // 決定要跳出通知

    // 根據用戶的選擇進行不同處理，這邊我指印出log結果
    deferredPrompt.userChoice.then(function(choiceResult) {
      console.log(choiceResult.outcome);

      if(choiceResult.outcome === 'dismissed'){
        console.log('User cancelled installation');
      }else{
        console.log('User added to home screen');
      }
    });
    deferredPrompt = null; // 一旦用戶允許加入後，之後就不會再出現通知
}
```

## 使用方式

先將 manifest.json 上述資料改為自己想要的設定，將manifest.json、service-worker.js 放置網頁根目錄，並開啟想要支援的網頁，以下使用小弟我[個人頁面](https://pin-yi.com
)來做示範。

1. 先至編輯器內將manifest.json放到head內

```html
<link rel="manifest" href="./manifest.json">
```
<br>

{{< image src="/images/pwa/1GveueD.png"  width="700" caption="範例頁面" src_s="/images/pwa/1GveueD.png" src_l="/images/pwa/1GveueD.png" >}}

2. 於程式最後新增以下

```js
 <script>
          if ('serviceWorker' in navigator)
          {
          window.addEventListener('load', function() {
            navigator.serviceWorker.register('./service-worker.js')
            .then(function() { console.log("Service Worker Registered, Cheers to PWA Fire!"); });
          }
          );
        }
    </script>
```
<br>

{{< image src="/images/pwa/U4gQDWr.png"  width="700" caption="範例頁面" src_s="/images/pwa/U4gQDWr.png" src_l="/images/pwa/U4gQDWr.png" >}}

 {{< admonition info "提醒" true >}}
manifest.json、service-worker.js　依據自己放置位置做更改😀
{{< /admonition >}}

## 如何使用

### IOS
* 手機：Iphone 12 Pro (IOS 14.5)
* 瀏覽器：Safari
	1. 先瀏覽範例網站小弟個人頁面。
	2. 點選下方中間分享圖示，選擇加入主畫面，按下新增，就會出現在手機主畫面，打開後，就會發現操作跟APP相同。

<br>

{{< image src="/images/pwa/1CnD18Q.jpg"  width="700" caption="範例頁面" src_s="/images/pwa/1CnD18Q.jpg" src_l="/images/pwa/1CnD18Q.jpg" >}}

### ANDROID
* 手機：OnePlus 8T (ANDROID 11)
* 瀏覽器：Chrome
	1. 先瀏覽範例網站小弟個人頁面。
	2. 安裝應用程式至手機，經過下載，就會出現在手機主畫面，打開後，就會發現操作跟APP相同。

<br>

{{< image src="/images/pwa/8rSTs1q.png"  width="700" caption="範例頁面" src_s="/images/pwa/8rSTs1q.png" src_l="/images/pwa/8rSTs1q.png" >}}	

<br>

## 成功畫面

{{< image src="/images/pwa/GcboFGA.png"  width="700" caption="範例頁面" src_s="/images/pwa/GcboFGA.png" src_l="/images/pwa/GcboFGA.png" >}}	

### 其他說明

 {{< admonition info "以上為備份筆記" true >}}
資料來源:

https://ithelp.ithome.com.tw/articles/10186584

https://ithelp.ithome.com.tw/articles/10188514
{{< /admonition >}}