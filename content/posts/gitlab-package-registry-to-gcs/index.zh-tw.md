---
weight: 4
title: "如何啟用 GitLab 的 Package Registry 以及將儲存庫改到 GCS 上"
date: 2022-12-29T17:40:00+08:00
lastmod: 2022-12-29T17:40:00+08:00
draft: false
author: "PinYi"
authorLink: "https://pin-yi.me"
description: ""
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["GitLab","Package Registry","GCS","GCP","實作"]
categories: ["codenotes"]

lightgallery: true

toc:
  auto: false
---

今天接到一個案子，之後 RD 部門想要使用 GitLab 的 Package Registry 功能來發布套件，且不想要把它存在 GitLab 伺服器上，希望可以直接存到 GCP 的 Google Cloud Storage 上，所以才有了此篇的筆記來記錄一下整個過程。

<br>

版本資訊
* GitLab 14.10 (有部分設定會於新版本棄用，請記得確認好自己的版本是否支援)

<br>

## 啟動 GitLab 的 Package Registry

首先我們當然要先啟動這項 Package Registry 功能才可以使用它，先說一下，我的 GitLab 是使用 docker-compose 來建置，所以後續的實作都會以 docker-compose 的方式來介紹，最後會帶一下，使用一般的啟動 GitLab 要怎麼設定它。

<br>

我們先看一下我的 GitLab docker-compose.yml 檔案：

```
version: '3'

services:
  gitlab:
    image: 'gitlab/gitlab-ee:14.10.5-ee.0'
    restart: always
    container_name: gitlab
    hostname: gitlab-pid
    logging:
      driver: "json-file"
      options:
        max-size: "100m"
        max-file: "50"
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url '${GITLAB_DOMAIN}'
        letsencrypt['enable'] = false
        gitlab_rails['initial_root_password'] = '${GITLAB_ROOT_PASSWORD}'
        gitlab_rails['gitlab_shell_ssh_port'] = '${GITLAB_HOST_SSH_PORT}'
        gitlab_rails['backup_keep_time'] = 79200
        gitlab_rails['omniauth_allow_single_sign_on'] = ['google_oauth2']
        gitlab_rails['omniauth_block_auto_created_users'] = false
        gitlab_rails['omniauth_sync_profile_from_provider'] = ['google_oauth2']
        gitlab_rails['omniauth_sync_profile_attributes'] = ['name', 'email']
        gitlab_rails['omniauth_providers'] = [
          {
		略過．．．
          }
        ]
    ports:
      - '${GITLAB_HOST_SSH_PORT}:22'
      - '${GITLAB_HOST_HTTP_PORT}:80'
      - '${GITLAB_HOST_HTTPS_PORT}:443'
    volumes:
    - './config:/etc/gitlab'
    - './logs:/var/log/gitlab'
    - './data:/var/opt/gitlab'
```

有些設定有略過或是省略不寫，大家就依照自己的設定來看就好～


<br>

## 參考資料

[GitLab Package Registry administration](https://docs.gitlab.com/14.10/ee/administration/packages/)