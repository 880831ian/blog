---
weight: 3
title: "主題文檔 - 內置 Shortcodes"
date: 2020-03-04T16:29:59+08:00
lastmod: 2020-03-04T16:29:59+08:00
draft: false
author: "FeelIt"
authorLink: "https://feelit.khusika.com"
description: "Hugo 提供了多個內置的 Shortcodes, 以方便作者保持 Markdown 內容的整潔."
resources:
- name: "featured-image"
  src: "featured-image.webp"
- name: "featured-image-preview"
  src: "featured-image-preview.webp"

tags: ["shortcodes"]
categories: ["documentation"]

lightgallery: true
hiddenFromHomePage: true


---

**Hugo** 提供了多個內置的 Shortcodes, 以方便作者保持 Markdown 內容的整潔.

<!--more-->

Hugo 使用 Markdown 為其簡單的內容格式. 但是, Markdown 在很多方面都無法很好地支持. 你可以使用純 HTML 來擴展可能性.

但這恰好是一個壞主意. 大家使用 Markdown, 正是因為它即使不經過渲染也可以輕松閱讀. 應該盡可能避免使用 HTML 以保持內容簡潔.

為了避免這種限制, Hugo 創建了 [shortcodes](https://gohugo.io/extras/shortcodes/).
shortcode 是一個簡單代碼段, 可以生成合理的 HTML 代碼, 並且符合 Markdown 的設計哲學.

Hugo 附帶了一組預定義的 shortcodes, 它們實現了一些非常常見的用法.
提供這些 shortcodes 是為了方便保持你的 Markdown 內容簡潔.

## 1 figure {#figure}

[`figure` 的文檔](https://gohugo.io/content-management/shortcodes#figure)

一個 `figure` 示例:

```markdown
{{</* figure src="/images/lighthouse.jpg" alt="/images/lighthouse.jpg" title="Lighthouse (figure)" */>}}
```

呈現的輸出效果如下:

{{< figure src="/images/lighthouse.jpg" alt="/images/lighthouse.jpg" title="Lighthouse (figure)" >}}

輸出的 HTML 看起來像這樣:

```html
<figure>
    <img src="/images/lighthouse.jpg" alt="/images/lighthouse.jpg" />
    <figcaption>
        <h4>Lighthouse (figure)</h4>
    </figcaption>
</figure>
```

## 2 gist

[`gist` 的文檔](https://gohugo.io/content-management/shortcodes#gist)

一個 `gist` 示例:

```markdown
{{</* gist spf13 7896402 */>}}
```

呈現的輸出效果如下:

{{< gist spf13 7896402 >}}

輸出的 HTML 看起來像這樣:

```html
<script type="application/javascript" src="https://gist.github.com/spf13/7896402.js"></script>
```

## 3 highlight

[`highlight` 的文檔](https://gohugo.io/content-management/shortcodes#highlight)

一個 `highlight` 示例:

```markdown
{{</* highlight html */>}}
<section id="main">
    <div>
        <h1 id="title">{{ .Title }}</h1>
        {{ range .Pages }}
            {{ .Render "summary"}}
        {{ end }}
    </div>
</section>
{{</* /highlight */>}}
```

呈現的輸出效果如下:

{{< highlight html >}}
<section id="main">
    <div>
        <h1 id="title">{{ .Title }}</h1>
        {{ range .Pages }}
            {{ .Render "summary"}}
        {{ end }}
    </div>
</section>
{{< /highlight >}}

## 4 instagram

{{< version 1.0.0 changed >}}

At the moment, Hugo using deprecated [oEmbed-legacy](https://developers.facebook.com/docs/instagram/oembed-legacy) linked API endpoint. Those deprecated API causes an error when Hugo retrieving the data. The newest API has been included in the [extended shortcode](https://feelit.khusika.com/theme-documentation-extended-shortcodes/#122-oembed-instagram).

## 5 param

[`param` 的文檔](https://gohugo.io/content-management/shortcodes#param)

一個 `param` 示例:

```markdown
{{</* param description */>}}
```

呈現的輸出效果如下:

{{< param description >}}

## 6 ref 和 relref {#ref-and-relref}

[`ref` 和 `relref` 的文檔](https://gohugo.io/content-management/shortcodes#ref-and-relref)

## 7 tweet

{{< version 1.0.1 changed >}}

This method was moved with the newest API in the [extended shortcode documentation](https://feelit.khusika.com/theme-documentation-extended-shortcodes/#123-oembed-twitter).

## 8 vimeo

[`vimeo` 的文檔](https://gohugo.io/content-management/shortcodes#vimeo)

一個 `vimeo` 示例:

```markdown
{{</* vimeo 146022717 */>}}
```

呈現的輸出效果如下:

{{< vimeo 146022717 >}}

## 9 youtube

[`youtube` 的文檔](https://gohugo.io/content-management/shortcodes#youtube)

一個 `youtube` 示例:

```markdown
{{</* youtube w7Ft2ymGmfc */>}}
```

呈現的輸出效果如下:

{{< youtube w7Ft2ymGmfc >}}

 
Ctrl+A or Ctrl+V 總共有：3627 字

 
在線上修改圖片，相片，照片So Easy. 點此看 »
線上工具 | Sitemap | 聯絡我們 | 繪文字 | 許願樹
Copyright ©2022 Online Tools,All Rights Reserved. TOP
GmailFacebookTwitterWhatsAppTelegramWeChatLine分享

 讀取中...