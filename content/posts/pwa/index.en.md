---
weight: 4
title: "PWA(Progressive Web App) Introduction and actual installation"
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

The full name of PWA is Progressive Web App, which is a progressive website application. It gradually optimizes the website to have the advantages of an APP. It has the following characteristics.


<!--more-->

## Introduce

The full name of PWA is Progressive Web App, which is a progressive website application. It gradually optimizes the website to have the advantages of an APP. It has the following characteristics.

* Progressive
* Progressive, providing every user to do basic browsing.
* Responsive
* Responsive user interface that can be optimized for different devices.
* Connectivity independent
* Does not depend on the network connection, through service workers, you can browse the website in a low bandwidth or even offline environment.
*App-like
* Let the website have the advantages of browsing speed like an APP, and provide a better user experience.
*Fresh
* Automatically update website content by service worker.
* Installable
* You can use Add To Home, just like an App, an icon will be added, you can directly add the website to the mobile phone desktop for switching use, no need to download and install through the App Store.

For details, please refer to [Official Website](https://developer.mozilla.org/zh-TW/docs/Web/Progressive_web_apps)
## Actual installation

{{< admonition tip "Interviewers should prepare the following frequently asked questions before going to the company for an interview" ture >}}
First download https://nas.pin-yi.me/sharing/nElXqbqyt (Place it on the NAS cloud)
There are two files in it, manifest.json and service-worker.js, which are introduced below.
{{</admonition >}}

### manifest.json (for a brief introduction, please refer to [official document](https://developer.mozilla.org/zh-TW/docs/Mozilla/Add-ons/WebExtensions/manifest.json))

````json
{
          "background_color": "#fff",
          "display": "standalone",
          "orientation":"portrait",
          "theme_color": "#fff",
          "short_name": "Short Name",
          "name": "name",
          "description": "Description",
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
````

* background_color background color
* Define the expected background color of the web application, which can create a smooth transition between the launch of the web application and the loading of content.
	
``` json
	"background_color": "#fff"
```
* display background color
* Define the developer's preferred web application display mode.

```` json
"display": "standalone"
````
Be
| Display Mode | Description |
| ---- | ---- |
| fullscreen | All available display area is filled and user agent chrome is not displayed. |
| standalone | This looks and feels like a standalone application, with different execution windows, icon application launchers...etc. In this mode, the user agent will not contain the control navigation bar, but can contain other UI elements such as the status bar. |
| minimal-ui | This looks and feels like a standalone app, but will have minimal settings to control the navigation bar UI elements, which will vary by browser. |
| browser | Default value. Applications are normally opened in a browser tab or in a new window, depending on the browser and platform. |

* orientation screen display orientation
* Define the default display direction, usually used in GAME, you may need to force the direction.
````json
"orientation": "portrait"
````
* theme_color website background color
* Set the theme color of each page of the website, such as changing the color of the URL.

````json
"theme_color": "#fff"
````

* short_name shortens the name
* Define the shortened name of the web application.

````json
"short_name": "Short Name"
````

* name name
* Define the name of the web application.

````json
"name": "name"
````
* name name
* Define the name of the web application.

````json
"name": "name"
````

* description description
* Provide a description to describe what this web application does.

````json
"description": "Description"
````

* lang language
* Define the language of the web application.

````json
"lang": "zh-TW"
````

* icons icon
* The object of the application icon.

````json
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
````

* start_url start URL
* Define where the web application starts.

````json
  "start_url": "./index.php"
````

* service-worker.js

````js
// This event is fired when the service worker is in the "installation phase"
self.addEventListener('install', function(event) {
     console.log('[Service Worker] Installing Service Worker ...', event);
});

// This event is fired when the service worker is in the "activation phase"
self.addEventListener('activate', function(event) {
    console.log('[Service Worker] Activating Service Worker ...', event);
    return self.clients.claim(); // This line is added to ensure that the service worker is loaded and activated correctly, or not
});
self.addEventListener('fetch', function(event) {
    console.log('[Service Worker] Fetch something ...', event);
    event.respondWith(fetch(event.request));
});
let deferredPrompt;

self.addEventListener('beforeinstallprompt', function(event) {
    console.log('beforeinstallprompt fired');
    event.preventDefault(); // Cancel the default direct jump notification setting
    deferredPrompt = event; // Pass the monitored install banner event to the deferredPrompt variable

    return false;
});
if(deferredPrompt) { // Make sure we have "intercepted" the install banner event from chrome
    deferredPrompt.prompt(); // decide to jump out of the notification

    // Different processing is performed according to the user's choice, here I will print out the log results
    deferredPrompt.userChoice.then(function(choiceResult) {
      console.log(choiceResult.outcome);

      if(choiceResult.outcome === 'dismissed'){
        console.log('User cancelled installation');
      }else{
        console.log('User added to home screen');
      }
    });
    deferredPrompt = null; // Once the user allows to join, no further notifications will appear
}
````
## How to use

First, change the above information in manifest.json to the settings you want, put manifest.json and service-worker.js in the root directory of the webpage, and open the webpage you want to support. The following uses my [personal page](https:/ /pin-yi.com
) as an example.

1. Go to the editor and put manifest.json in the head

```html
<link rel="manifest" href="./manifest.json">
````
<br>

{{< image src="/images/pwa/1GveueD.png" width="700" caption="Example page" src_s="/images/pwa/1GveueD.png" src_l="/images/pwa/1GveueD.png" >}}

2. Add the following at the end of the program

````js
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
````
<br>

{{< image src="/images/pwa/U4gQDWr.png" width="700" caption="Example page" src_s="/images/pwa/U4gQDWr.png" src_l="/images/pwa/U4gQDWr.png" >}}

 {{< admonition info "Alert" true >}}
Manifest.json, service-worker.js Make changes according to your own placement 😀
{{</admonition >}}

## how to use

### IOS

* Mobile: Iphone 12 Pro (IOS 14.5)
* Browser: Safari
1. First browse the personal page of the example website.
2. Click the share icon in the middle of the bottom, choose to add to the main screen, press Add, it will appear on the main screen of the mobile phone, after opening, you will find that the operation is the same as the APP.

<br>

{{< image src="/images/pwa/1CnD18Q.jpg" width="700" caption="Sample page" src_s="/images/pwa/1CnD18Q.jpg" src_l="/images/pwa/1CnD18Q.jpg" >}}

### ANDROID
* Mobile: OnePlus 8T (ANDROID 11)
* Browser: Chrome
1. First browse the personal page of the example website.
2. Install the APP to the mobile phone. After downloading, it will appear on the main screen of the mobile phone. After opening, you will find that the operation is the same as the APP.

<br>

{{< image src="/images/pwa/8rSTs1q.png" width="700" caption="Example page" src_s="/images/pwa/8rSTs1q.png" src_l="/images/pwa/8rSTs1q.png" >}}

<br>

## Success screen

{{< image src="/images/pwa/GcboFGA.png" width="700" caption="Sample page" src_s="/images/pwa/GcboFGA.png" src_l="/images/pwa/GcboFGA.png" >}}

### other instructions

 {{< admonition info "Above are backup notes" true >}}
source:

https://ithelp.ithome.com.tw/articles/10186584

https://ithelp.ithome.com.tw/articles/10188514
{{</admonition >}}